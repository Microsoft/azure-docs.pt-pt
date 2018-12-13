---
title: Gerir, registar, implementar e monitorizar os modelos de ML
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o serviço do Azure Machine Learning para implementar, gerir e monitorizar os seus modelos para melhorar continuamente. Pode implementar os modelos treinados com o serviço do Azure Machine Learning, no seu computador local ou de outras origens.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 25f149ad4df43a7e5b443d6abd72be91072cb47f
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250213"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Gerir, implementar e monitorizar os modelos de serviço do Azure Machine Learning

Neste artigo, pode saber como utilizar o serviço do Azure Machine Learning para implementar, gerir e monitorizar os seus modelos para melhorar continuamente. Pode implementar os modelos treinados com o Azure Machine Learning, no seu computador local ou de outras origens. 

O diagrama seguinte ilustra o fluxo de trabalho de implantação completa: [ ![Fluxo de trabalho de implantação para o Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

O fluxo de trabalho de implantação inclui os seguintes passos:
1. **Registe o modelo** num registo alojado na sua área de trabalho do serviço do Azure Machine Learning
1. **Registe-se de uma imagem** que emparelha um modelo com um script de classificação e dependências num contêiner portátil 
1. **Implementar** a imagem como um serviço da web na cloud ou para dispositivos de ponta
1. **Monitorizar e recolher dados**

Cada passo pode ser efetuado de forma independente ou como parte de um comando de implementação única. Além disso, pode integrar a implantação num **fluxo de trabalho de CI/CD** conforme ilustrado neste gráfico.

[ !["O azure Machine Learning integração contínua/contínua (CI/CD) ciclo de implantação"](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)


## <a name="step-1-register-model"></a>Passo 1: Registar o modelo

O registo de modelo mantém um registo de todos os modelos na sua área de trabalho do serviço do Azure Machine Learning.
Modelos são identificados pelo nome e versão. Sempre que registar um modelo com o mesmo nome que um já existente, o registro incrementa a versão. Também pode fornecer marcas de metadados adicionais durante o registo que pode ser utilizado ao pesquisar para modelos.

Não é possível eliminar modelos que estão a ser utilizados por uma imagem.

## <a name="step-2-register-image"></a>Passo 2: Registe-se a imagem

Imagens permitem a implementação do modelo fiável, juntamente com todos os componentes necessários para usar o modelo. Uma imagem contém os seguintes itens:

* O modelo
* O motor de classificação
* O ficheiro de classificação ou a aplicação
* Todas as dependências necessárias para o modelo de pontuação

A imagem também pode incluir os componentes do SDK para o registo e monitorização. Os dados de registos do SDK podem servir para ajustar ou voltar a preparar seu modelo, incluindo a entrada e saída do modelo.

O Azure Machine Learning suporta as estruturas mais populares, mas em geral pode trabalhar qualquer arquitetura que pode ser o pip instalados.

Em sua área de trabalho foi criada, por isso, foram outros vários outros recursos do Azure utilizados por essa área de trabalho.
Todos os objetos usados para criar a imagem são armazenados na conta de armazenamento do Azure na sua área de trabalho. A imagem é criada e armazenada no Azure Container Registry. Pode fornecer as marcas de metadados adicionais durante a criação de imagem, que também são armazenados pelo registo de imagem e pode ser consultada para localizar a sua imagem.

## <a name="step-3-deploy-image"></a>Passo 3: Implementar a imagem

Pode implantar imagens registadas para a cloud ou para dispositivos periféricos. O processo de implantação cria todos os recursos necessários para monitorizar, balanceamento de carga e dimensionamento automático seu modelo. Acesso aos serviços implementados pode ser protegido com autenticação baseada em certificado ao fornecer os recursos de segurança durante a implementação. Também é possível atualizar uma implementação existente para utilizar uma imagem mais recente.

Implementações de serviços da Web também podem ser pesquisadas. Por exemplo, pode procurar todas as implementações de um modelo específico ou a imagem.

[ ![Destinos de inferência](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Pode implantar suas imagens para o seguinte [destinos de implementação](how-to-deploy-and-where.md) na cloud:

* Instância de Contentor do Azure
* Serviço Kubernetes do Azure
* Máquinas FPGA do Azure
* Dispositivos do IoT Edge do Azure

Como o seu serviço é implementado, o pedido de inferência é automaticamente com balanceamento de carga e o cluster for aumentado para satisfazer picos a pedido. [Telemetria sobre o seu serviço pode ser capturada](how-to-enable-app-insights.md) no serviço do Azure Application Insights associado à sua área de trabalho.

## <a name="step-4-monitor-models-and-collect-data"></a>Passo 4: Modelos de monitorizar e recolher dados

Um SDK para captura de registo e dados de modelo estará disponível, para que possa monitorizar a entrada, saída e outros dados relevantes do seu modelo. Os dados são armazenados como um blob na conta de armazenamento do Azure para a área de trabalho.

Para utilizar o SDK com o seu modelo, é possível importar o SDK no seu script de classificação ou a aplicação. Em seguida, pode utilizar o SDK para registos de dados como parâmetros, os resultados ou detalhes de entrada.

Se decidir [ativar a recolha de dados de modelo](how-to-enable-data-collection.md) sempre que implanta a imagem, os detalhes necessários para capturar os dados, como as credenciais para seu armazenamento de BLOBs pessoal, são provisionados automaticamente.

> [!Important]
> Microsoft não vê os dados coletados do seu modelo. Os dados são enviados diretamente para a conta de armazenamento do Azure para a área de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [como e onde pode implementar modelos](how-to-deploy-and-where.md) com o serviço Azure Machine Learning.
