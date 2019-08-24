---
title: 'MLOps: Gerenciar, implantar & monitorar modelos de ML'
titleSuffix: Azure Machine Learning service
description: 'Saiba como usar o serviço de Azure Machine Learning para MLOps: implantar, gerenciar e monitorar seus modelos para melhorá-los continuamente. Pode implementar os modelos treinados com o serviço do Azure Machine Learning, no seu computador local ou de outras origens.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 270ebb6247e812d42ac84698d0073b41c639cb9a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011594"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Gerir, implementar e monitorizar os modelos de serviço do Azure Machine Learning

Neste artigo, saiba mais sobre como usar Azure Machine Learning serviço para gerenciar o ciclo de vida de seus modelos. O Azure Machine Learning usa uma abordagem de MLOps (operações de Machine Learning), que melhora a qualidade e a consistência de suas soluções de aprendizado de máquina. 

Azure Machine Learning serviço fornece os seguintes recursos de MLOps:

- **Implantar projetos ML de qualquer lugar**
- **Monitorar aplicativos ml para problemas operacionais e relacionados ao ml** – comparar entradas de modelo entre treinamento e inferência, explorar métricas específicas de modelo e fornecer monitoramento e alertas em sua infraestrutura de ml.
- **Capture os dados necessários para estabelecer uma trilha de auditoria de ponta a ponta do ciclo de vida am**, incluindo quem está publicando modelos, por que as alterações estão sendo feitas e quando os modelos foram implantados ou usados na produção.
- **Automatize o ciclo de vida de ml de ponta a ponta com Azure Machine Learning e o Azure DevOps** para atualizar modelos com frequência, testar novos modelos e distribuir continuamente novos modelos de ml junto com seus outros aplicativos e serviços.

Para saber mais sobre os conceitos por trás do MLOps e como eles se aplicam ao serviço Azure Machine Learning, Assista ao vídeo a seguir.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>Implantar projetos ML de qualquer lugar

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Transforme seu processo de treinamento em um pipeline reproduzível
Use pipelines de ML de Azure Machine Learning para reunir todas as etapas envolvidas em seu processo de treinamento de modelo, da preparação de dados para a extração de recursos até o ajuste de hiperparâmetro até a avaliação do modelo.

Para obter mais informações, consulte pipelines do [ml](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>Registrar e acompanhar modelos de ML

O registro de modelo permite armazenar e fazer a versão de seus modelos na nuvem do Azure, no seu espaço de trabalho. O registro de modelo torna mais fácil organizar e controlar seus modelos treinados.

> [!TIP]
> Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo em seu espaço de trabalho do Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que foram registrados.
 
Os modelos registrados são identificados por nome e versão. Sempre que registar um modelo com o mesmo nome que um já existente, o registro incrementa a versão. Também pode fornecer marcas de metadados adicionais durante o registo que pode ser utilizado ao pesquisar para modelos. O serviço Azure Machine Learning dá suporte a qualquer modelo que possa ser carregado usando Python 3.5.2 ou superior.

> [!TIP]
> Você também pode registrar modelos treinados fora do serviço de Azure Machine Learning.

Você não pode excluir um modelo registrado que está sendo usado em uma implantação ativa.
Para obter mais informações, consulte a seção registrar modelo de [implantar modelos](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Modelos de pacote e depuração

Antes de implantar um modelo na produção, ele é empacotado em uma imagem do Docker. Na maioria dos casos, a criação de imagem ocorre automaticamente em segundo plano durante a implantação. Para cenários avançados, você pode especificar manualmente a imagem.

Se você tiver problemas com a implantação, poderá implantar em seu ambiente de desenvolvimento local para solução de problemas e depuração.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md#registermodel) e [solucionar problemas](how-to-troubleshoot-deployment.md)de implantações.

### <a name="validate-and-profile-models"></a>Validar e criar perfil de modelos

Azure Machine Learning serviço pode usar a criação de perfil para determinar as configurações de CPU e memória ideais a serem usadas ao implantar seu modelo. A validação de modelo ocorre como parte desse processo, usando dados que você fornece para o processo de criação de perfil.

### <a name="convert-and-optimize-models"></a>Converter e otimizar modelos

A conversão do modelo para [abrir a rede neural](https://onnx.ai) (ONNX) pode melhorar o desempenho. Em média, a conversão para ONNX pode gerar um aumento de desempenho de 2x.

Para obter mais informações sobre o ONNX com o serviço Azure Machine Learning, consulte o artigo [criar e acelerar modelos de ml](concept-onnx.md) .

### <a name="use-models"></a>Usar modelos

Modelos de aprendizado de máquina treinados podem ser implantados como serviços Web na nuvem ou localmente em seu ambiente de desenvolvimento. Você também pode implantar modelos para Azure IoT Edge dispositivos. As implantações podem usar a CPU, GPU ou FPGA (matrizes de portão programável por campo) para inferência. Você também pode usar modelos do Power BI.

Ao usar um modelo como um serviço Web ou IoT Edge dispositivo, você fornece os seguintes itens:

* Os modelos que são usados para pontuar os dados enviados para o serviço/dispositivo.
* Um script de entrada. Esse script aceita solicitações, usa os modelos para pontuar os dados e retornar uma resposta.
* Um arquivo de ambiente Conda que descreve as dependências exigidas pelos modelos e o script de entrada.
* Quaisquer ativos adicionais, como texto, dados etc., que são necessários para o (s) modelos e o script de entrada.

Esses ativos são empacotados em uma imagem do Docker e implantados como um serviço Web ou IoT Edge módulo.

Opcionalmente, você pode usar os seguintes parâmetros para ajustar ainda mais a implantação:

* Habilitar GPU: Usado para habilitar o suporte de GPU na imagem do Docker. A imagem deve ser usada em Microsoft Azure serviços como instâncias de contêiner do Azure, serviço kubernetes do Azure, computação de Azure Machine Learning ou máquinas virtuais do Azure.
* Etapas de arquivo adicionais do Docker: Um arquivo que contém etapas adicionais do Docker para executar ao criar a imagem do Docker.
* Imagem base: Uma imagem personalizada a ser usada como a imagem de base. Se você não usar uma imagem personalizada, a imagem base será fornecida pelo serviço de Azure Machine Learning.

Você também fornece a configuração da plataforma de implantação de destino. Por exemplo, o tipo de família de VMs, a memória disponível e o número de núcleos durante a implantação no serviço kubernetes do Azure.

Quando a imagem é criada, os componentes exigidos pelo serviço Azure Machine Learning também são adicionados. Por exemplo, os ativos necessários para executar o serviço Web e interagir com IoT Edge.

> [!NOTE]
> Você não pode modificar ou alterar o servidor Web ou os componentes de IoT Edge usados na imagem do Docker. O serviço de Azure Machine Learning usa uma configuração de servidor Web e componentes de IoT Edge que são testados e suportados pela Microsoft.

#### <a name="web-service"></a>Serviço Web

Você pode usar seus modelos em **Serviços Web** com os seguintes destinos de computação:

* Instância de Contentor do Azure
* Serviço Kubernetes do Azure
* Ambiente de desenvolvimento local

Para implantar o modelo como um serviço Web, você deve fornecer os seguintes itens:

* O modelo ou Ensemble de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita solicitações e invoca o modelo, dependências Conda, etc.
* Configuração de implantação que descreve como e onde implantar o modelo.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Dispositivos do IoT Edge

Você pode usar modelos com dispositivos IoT por meio de **módulos Azure IOT Edge**. IoT Edge módulos são implantados em um dispositivo de hardware, o que permite a inferência, ou a Pontuação do modelo, no dispositivo.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md).

### <a name="analytics"></a>Análise

O Microsoft Power BI dá suporte ao uso de modelos de aprendizado de máquina para análise de dados. Para obter mais informações, consulte [integração de Azure Machine Learning no Power bi (versão prévia)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Monitorar aplicativos ML para problemas operacionais e relacionados ao ML

O monitoramento permite que você entenda quais dados estão sendo enviados para seu modelo e as previsões que ele retorna.

Essas informações ajudam a entender como seu modelo está sendo usado. Os dados de entrada coletados também podem ser úteis para treinar versões futuras do modelo.

Para obter mais informações, consulte [como habilitar a coleta de dados de modelo](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Capturar uma trilha de auditoria de ponta a ponta do ciclo de vida de ML

O Azure ML oferece a capacidade de acompanhar a trilha de auditoria de ponta a ponta de todos os seus ativos de ML. Especificamente:

- O Azure ML [integra-se com o Git](how-to-set-up-training-targets.md#gitintegration) para controlar as informações das quais o repositório/Branch/confirmação do seu código provém.
- Os conjuntos de dados [do Azure ml](how-to-create-register-datasets.md) ajudam você a controlar e a obter a versão.
- O histórico de execuções do Azure ML armazena um instantâneo do código, dos dados e da computação usados para treinar um modelo.
- O registro de modelo do Azure ML captura todos os metadados associados ao seu modelo (que experimento o treina, onde ele está sendo implantado, se suas implantações estiverem íntegras).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>Automatizar o ciclo de vida de AM de ponta a ponta 

Você pode usar o GitHub e o Azure Pipelines para criar um processo de integração contínua que treina um modelo. Em um cenário típico, quando um cientista de dados verifica uma alteração no repositório Git para um projeto, o pipeline do Azure iniciará uma execução de treinamento. Os resultados da execução podem ser inspecionados para ver as características de desempenho do modelo treinado. Você também pode criar um pipeline que implanta o modelo como um serviço Web.

A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) torna mais fácil trabalhar com Azure pipelines. Ele fornece os seguintes aprimoramentos para Azure Pipelines:

* Habilita a seleção de espaço de trabalho ao definir uma conexão de serviço.
* Permite que os pipelines de versão sejam disparados por modelos treinados criados em um pipeline de treinamento.

Para obter mais informações sobre como usar Azure Pipelines com Azure Machine Learning, consulte a [integração contínua e a implantação de modelos de ml com Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning) artigo e o repositório do [Azure Machine Learning Service MLOps](https://aka.ms/mlops) .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [como e onde pode implementar modelos](how-to-deploy-and-where.md) com o serviço Azure Machine Learning. Para obter um exemplo de implantação, [consulte Tutorial: Implante um modelo de classificação de imagem em instâncias](tutorial-deploy-models-with-aml.md)de contêiner do Azure.

Saiba como criar [integração e implantação contínuas de modelos de ml com Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning). 

Saiba como criar aplicativos cliente e serviços que consomem [um modelo implantado como um serviço Web](how-to-consume-web-service.md).
