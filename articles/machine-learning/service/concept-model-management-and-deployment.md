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
ms.openlocfilehash: 18a80af8422e30ce3e87395449fca7b5f6a73762
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025020"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Gerir, implementar e monitorizar os modelos de serviço do Azure Machine Learning

Neste artigo, pode saber como utilizar o serviço do Azure Machine Learning para implementar, gerir e monitorizar os seus modelos para melhorar continuamente. Pode implementar os modelos treinados com o Azure Machine Learning, no seu computador local ou de outras origens. 

O diagrama seguinte ilustra o fluxo de trabalho de implantação completa: [![Fluxo de trabalho de implantação para o Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

O MLOps / fluxo de trabalho de implantação inclui os seguintes passos:
1. **Registe o modelo** num registo alojado na sua área de trabalho do serviço do Azure Machine Learning
1. **Utilize** o modelo de um serviço da web na cloud, num dispositivo IoT ou para a análise com o Power BI.
1. **Monitorizar e recolher dados**
1. **Atualização** uma implementação para utilizar uma nova imagem.

Cada passo pode ser efetuado de forma independente ou como parte de um único comando. Além disso, pode criar uma **fluxo de trabalho de CI/CD** conforme ilustrado neste gráfico.

[!["O azure Machine Learning integração contínua/contínua (CI/CD) ciclo de implantação"](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.youtube.com/embed/0MaHb070H_8]

## <a name="step-1-register-model"></a>Passo 1: Registar o modelo

Registo do modelo permite-lhe armazenar e versão seus modelos na cloud do Azure, na sua área de trabalho. O registo de modelo torna mais fácil organizar e manter o controle de seus modelos de formação.
 
Modelos registrados são identificados pelo nome e versão. Sempre que registar um modelo com o mesmo nome que um já existente, o registro incrementa a versão. Também pode fornecer marcas de metadados adicionais durante o registo que pode ser utilizado ao pesquisar para modelos. O serviço do Azure Machine Learning suporta qualquer modelo que pode ser carregados com o Python 3.5.2 ou superior.

Não é possível eliminar modelos que estão a ser utilizados numa implementação ativa.

Para obter mais informações, consulte a secção de modelo de registro de [implementar modelos](how-to-deploy-and-where.md#registermodel).

Para obter um exemplo de um modelo armazenado no formato de pickle de registro, consulte [Tutorial: Preparar um modelo de classificação de imagem](tutorial-deploy-models-with-aml.md).

## <a name="step-2-use-the-model"></a>Passo 2: Utilizar o modelo

Modelos de aprendizagem automática podem ser utilizados como um serviço da web, em dispositivos IoT Edge, ou para a análise de serviços como o Power BI.

### <a name="web-service"></a>Serviço Web

Pode utilizar os seus modelos no **serviços da web** destinos de computação com o seguinte:

* Instância de Contentor do Azure
* Azure Kubernetes Service

Para implementar o modelo como um serviço web, tem de fornecer o seguinte:

* O modelo ou ensemble de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita os pedidos e invoca o modelo, as dependências de conda, etc.
* Configuração de implementação que descreve como e onde implementar o modelo.

Para obter mais informações, consulte [implementar modelos](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Dispositivos do IoT Edge

Pode utilizar modelos com dispositivos de IoT através de **módulos do Azure IoT Edge**. Módulos do IoT Edge são implementados em dispositivos de hardware, que permite que a inferência no dispositivo.

Para obter mais informações, consulte [implementar modelos](how-to-deploy-and-where.md).

### <a name="analytics"></a>Análise

Microsoft Power BI suporta usando modelos de machine learning para análise de dados. Para obter mais informações, consulte [integração do Azure Machine Learning no Power BI (pré-visualização)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="step-3-monitor-models-and-collect-data"></a>Passo 3: Modelos de monitorizar e recolher dados

Monitorização permite-lhe compreender os dados que estão a ser enviados para o seu modelo e as previsões que devolve.

Estas informações ajudam-o a compreender como o seu modelo está a ser utilizado. Os dados recolhidos de entrada também poderão ser útil em versões futuras do treinamento do modelo.

Para obter mais informações, consulte [como ativar a recolha de dados de modelo](how-to-enable-data-collection.md).

## <a name="step-4-update-the-deployment"></a>Passo 4: A implementação de atualização

As implementações têm de ser atualizadas explicitamente. Para obter mais informações, consulte atualizar seção [implementar modelos](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [como e onde pode implementar modelos](how-to-deploy-and-where.md) com o serviço Azure Machine Learning. Para obter um exemplo de implementação, consulte [Tutorial: Implementar um modelo de classificação de imagem no Azure Container Instances](tutorial-deploy-models-with-aml.md).

Saiba como criar aplicações de cliente e serviços a que [consumir um modelo implementado como um serviço web](how-to-consume-web-service.md).
