---
title: 'MLOps: Gerir, implementar e monitorizar os modelos de ML'
titleSuffix: Azure Machine Learning service
description: 'Saiba como utilizar o serviço do Azure Machine Learning para MLOps: implementar, gerir e monitorizar os seus modelos para melhorar continuamente. Pode implementar os modelos treinados com o serviço do Azure Machine Learning, no seu computador local ou de outras origens.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 5cbb7f13214a86f528521fdeb1ffa1374ca813ef
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331696"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Gerir, implementar e monitorizar os modelos de serviço do Azure Machine Learning

Neste artigo, saiba mais sobre como utilizar o serviço Azure Machine Learning para gerir o ciclo de vida dos seus modelos. O Azure Machine Learning usa uma abordagem de operações de Aprendizado de máquina (MLOps), o que melhora a qualidade e consistência das suas soluções de aprendizagem automática. Serviço do Azure Machine Learning fornece as seguintes capacidades de MLOps:

* Integração com Pipelines do Azure. Defina fluxos de integração e implementação contínuos para seus modelos.
* Um registo de modelo que mantém várias versões dos seus modelos treinados.
* Validação de modelo. Automaticamente validar seus modelos de formação e selecione a configuração ideal para implementá-los em produção.
* Implemente os seus modelos como um serviço web na cloud, localmente ou para dispositivos do IoT Edge.
* Monitorize o desempenho de seu modelo implementado, pelo que pode promover a melhorias na próxima versão do modelo.

Para saber mais sobre os conceitos por trás MLOps e como eles se aplicam ao serviço Azure Machine Learning, veja o vídeo seguinte.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="integration-with-azure-pipelines"></a>Integração com Pipelines do Azure

Pode usar Pipelines do Azure para criar um processo de integração contínua, que prepara um modelo. Num cenário típico, quando um cientista de dados verifica uma alteração no repositório Git para um projeto, o Pipeline do Azure irá iniciar uma execução de treinamento. Os resultados da execução, em seguida, podem ser inspecionados para ver as características de desempenho do modelo preparado. Também pode criar um pipeline que implementa o modelo como um serviço web.

O [extensão do Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) torna mais fácil trabalhar com os Pipelines do Azure. Fornece os seguintes melhoramentos para os Pipelines do Azure:

* Permite a seleção de área de trabalho ao definir uma ligação de serviço.
* Permite lançar pipelines para ser acionada por modelos de formação criados num pipeline de treinamento.

Para obter mais informações sobre a utilização de Pipelines do Azure com o Azure Machine Learning, consulte a [integração contínua e implementação de modelos de ML com Pipelines do Azure](/azure/devops/pipelines/targets/azure-machine-learning) artigo e o [MLOps do serviço do Azure Machine Learning](https://aka.ms/mlops) repositório.

## <a name="convert-and-optimize-models"></a>Converter e otimizar modelos

Converter o seu modelo para [aberto Exchange de rede Neural](https://onnx.ai) (ONNX) pode melhorar o desempenho. Em média, a conversão para ONNX pode gerar um 2x no aumento de desempenho.

Para obter mais informações sobre ONNX com o serviço Azure Machine Learning, consulte a [criar e acelerar a modelos de ML](concept-onnx.md) artigo.

## <a name="register-models"></a>Registe-se de modelos

Registo do modelo permite-lhe armazenar e versão seus modelos na cloud do Azure, na sua área de trabalho. O registo de modelo torna mais fácil organizar e manter o controle de seus modelos de formação.

> [!TIP]
> Um modelo registado é um contentor lógico para um ou mais arquivos que formam o seu modelo. Por exemplo, se tiver um modelo que está armazenado em vários arquivos, pode registá-los como um modelo único na sua área de trabalho do Azure Machine Learning. Depois do Registro, pode, em seguida, transferir ou implementar o modelo registado e receber todos os ficheiros que foram registados.
 
Modelos registrados são identificados pelo nome e versão. Sempre que registar um modelo com o mesmo nome que um já existente, o registro incrementa a versão. Também pode fornecer marcas de metadados adicionais durante o registo que pode ser utilizado ao pesquisar para modelos. O serviço do Azure Machine Learning suporta qualquer modelo que pode ser carregados com o Python 3.5.2 ou superior.

> [!TIP]
> Também pode registar modelos com base fora o serviço Azure Machine Learning.

Não é possível eliminar um modelo registado que está a ser utilizado numa implementação ativa.

Para obter mais informações, consulte a secção de modelo de registro de [implementar modelos](how-to-deploy-and-where.md#registermodel).

Para obter um exemplo de um modelo armazenado no formato de pickle de registro, consulte [Tutorial: Preparar um modelo de classificação de imagem](tutorial-deploy-models-with-aml.md).

## <a name="package-and-debug-models"></a>Modelos de pacote e de depuração

Antes de implementar um modelo em produção, é empacotado numa imagem do Docker. Na maioria dos casos, criação de imagens ocorre automaticamente em segundo plano durante a implementação. Para cenários avançados, pode especificar manualmente a imagem.

Caso se depare com problemas com a implementação, pode implementar no seu ambiente de desenvolvimento local para resolução de problemas e depuração.

Para obter mais informações, consulte [implementar modelos](how-to-deploy-and-where.md#registermodel) e [resolver problemas de implementações](how-to-troubleshoot-deployment.md).

## <a name="validate-and-profile-models"></a>Validar e modelos de perfil

O serviço de Machine Learning do Azure pode utilizar a criação de perfis de mensagens em fila para determinar as definições de CPU e memória ideais para utilizar quando implementar o seu modelo. Validação de modelo ocorre como parte deste processo, com dados de que fornecer para o processo de criação de perfis.

## <a name="use-models"></a>Utilizar modelos

Treinado modelos de machine learning podem ser implementados como serviços da web na nuvem ou localmente no seu ambiente de desenvolvimento. Também pode implementar modelos de dispositivos do Azure IoT Edge. Implementações podem utilizar a CPU, GPU ou matrizes de porta de campos programáveis (FPGA) para inferência. Também pode utilizar modelos do Power BI.

Quando utiliza um modelo como um serviço web ou um dispositivo IoT Edge, é fornecer os seguintes itens:

* O modelo (s) que é utilizados para classificar os dados enviados para o serviço/dispositivo.
* Um script de entrada. Este script aceita os pedidos, utiliza o modelo (s) para classificar os dados e devolver uma resposta.
* Um ficheiro de ambiente de conda que descreve as dependências necessárias pelo script de modelo (s) e de entrada.
* Quaisquer recursos adicionais, como texto, dados, etc. que são necessários para o script de modelo (s) e de entrada.

Esses ativos são empacotados numa imagem do Docker e implementados como um serviço web ou o módulo do IoT Edge.

Opcionalmente, pode utilizar os seguintes parâmetros para otimizar ainda mais a implementação:

* Enable GPU: Utilizado para ativar o suporte GPU na imagem do Docker. A imagem tem de ser utilizada em serviços do Microsoft Azure, como o Azure Container Instances, serviço Kubernetes do Azure, a computação do Azure Machine Learning ou máquinas virtuais do Azure.
* Passos de ficheiro do extra docker: Um ficheiro que contém passos adicionais de Docker para executar ao criar a imagem do Docker.
* Imagem de base: Uma imagem personalizada para utilizar como a imagem base. Se não utilizar uma imagem personalizada, a imagem base é fornecida pelo serviço Azure Machine Learning.

Também fornecer a configuração da plataforma de implementação de destino. Por exemplo, a VM tipo de família, memória disponível e número de núcleos quando implementar no serviço Kubernetes do Azure.

Quando a imagem é criada, os componentes necessários pelo serviço do Azure Machine Learning também são adicionadas. Por exemplo, recursos necessários para executar o serviço web e interagir com o IoT Edge.

> [!NOTE]
> Não é possível modificar ou alterar os componentes de IoT Edge utilizadas na imagem de Docker ou do servidor web. O serviço do Azure Machine Learning utiliza uma configuração de servidor web e componentes do IoT Edge testados e suportados pela Microsoft.

### <a name="web-service"></a>Serviço Web

Pode utilizar os seus modelos no **serviços da web** destinos de computação com o seguinte:

* Instância de Contentor do Azure
* Serviço Kubernetes do Azure
* Ambiente de desenvolvimento local

Para implementar o modelo como um serviço web, tem de fornecer os seguintes itens:

* O modelo ou ensemble de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita os pedidos e invoca o modelo, as dependências de conda, etc.
* Configuração de implementação que descreve como e onde implementar o modelo.

Para obter mais informações, consulte [implementar modelos](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Dispositivos IoT Edge


Pode utilizar modelos com dispositivos de IoT através de **módulos do Azure IoT Edge**. Módulos do IoT Edge são implementados num dispositivo de hardware, que permite que a inferência de tipos ou modelo de classificação, no dispositivo.

Para obter mais informações, consulte [implementar modelos](how-to-deploy-and-where.md).

### <a name="analytics"></a>Análise

Microsoft Power BI suporta usando modelos de machine learning para análise de dados. Para obter mais informações, consulte [integração do Azure Machine Learning no Power BI (pré-visualização)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="monitor-and-collect-data"></a>Monitorizar e recolher dados

Monitorização permite-lhe compreender os dados que estão a ser enviados para o seu modelo e as previsões que devolve.

Estas informações ajudam-o a compreender como o seu modelo está a ser utilizado. Os dados recolhidos de entrada também poderão ser útil em versões futuras do treinamento do modelo.

Para obter mais informações, consulte [como ativar a recolha de dados de modelo](how-to-enable-data-collection.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [como e onde pode implementar modelos](how-to-deploy-and-where.md) com o serviço Azure Machine Learning. Para obter um exemplo de implementação, consulte [Tutorial: Implementar um modelo de classificação de imagem no Azure Container Instances](tutorial-deploy-models-with-aml.md).

Saiba como criar [integração contínua e implementação de modelos de ML com Pipelines do Azure](/azure/devops/pipelines/targets/azure-machine-learning). 

Saiba como criar aplicações de cliente e serviços a que [consumir um modelo implementado como um serviço web](how-to-consume-web-service.md).
