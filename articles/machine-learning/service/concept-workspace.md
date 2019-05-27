---
title: O que é uma área de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba o que uma área de trabalho é e por que precisa de um para o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 5785bf8f6538f1d91e7a23178e29487ebee14f29
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989837"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>O que é uma área de trabalho do Azure Machine Learning?

A área de trabalho é o recurso de nível superior para o serviço Azure Machine Learning. Ele fornece um local centralizado para trabalhar com todos os artefactos que criar quando utilizar o serviço Azure Machine Learning.

A área de trabalho mantém um histórico de execuções de preparação, incluindo registos, métricas, saída e um instantâneo dos seus scripts. Utilize estas informações para determinar qual execução de treinamento produz o melhor modelo.  

Depois de ter um modelo de que gosta, registe-o com a área de trabalho. Utilize o modelo registado e os scripts de classificação para implementar no Azure Container Instances, Azure Kubernetes Service, ou uma matriz de porta de campos programáveis (FPGA) como um ponto de final HTTP baseado em REST. Também pode implementar o modelo para um dispositivo Azure IoT Edge como um módulo.

## <a name="taxonomy"></a>Taxonomia 

Uma taxonomia da área de trabalho é ilustrada no diagrama seguinte:

[![Taxonomia da área de trabalho](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

O diagrama mostra os seguintes componentes de uma área de trabalho:

+ Uma área de trabalho pode conter [VMs de bloco de notas](quickstart-run-cloud-notebook.md), recursos configurados com o ambiente do Python necessário para executar o Azure Machine Learning na cloud.
+ [Funções de utilizador](how-to-assign-roles.md) permitir que Compartilhe sua área de trabalho com outros utilizadores, as equipes ou projetos.
+ [Destinos de computação](concept-azure-machine-learning-architecture.md#compute-target) são utilizados para executar as suas experimentações.
+ Quando cria a área de trabalho, [recursos associados](#resources) também são criados por si.
+ [Experimentações](concept-azure-machine-learning-architecture.md#experiment) execuções de treinamento que utilizar para criar os seus modelos.  Pode criar e executar experimentações com
    + O [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + O [automatizada experiências de machine learning (pré-visualização)](how-to-create-portal-experiments.md) secção no portal do Azure.
    + O [interface visual (pré-visualização)](ui-concept-visual-interface.md).
+ [Pipelines](concept-azure-machine-learning-architecture.md#pipeline) são fluxos de trabalho reutilizáveis para formação e reparametrização do seu modelo.
+ [Conjuntos de dados](concept-azure-machine-learning-architecture.md#dataset) ajuda na gestão dos dados que utilizar para a criação de treinamento e o pipeline de modelo.
+ Depois de ter um modelo que pretende implementar, criar um [modelo registado](concept-azure-machine-learning-architecture.md#model-registry).
+ Utilizar o modelo registado e um script de classificação para criar uma [implementação](concept-azure-machine-learning-architecture.md#image-registry).

## <a name="tools-for-workspace-interaction"></a>Ferramentas para interação de área de trabalho

Pode interagir com a sua área de trabalho das seguintes formas:

+ Na web:
    + O [portal do Azure](https://azure.portal.com)
    + O [interface visual (pré-visualização)](ui-concept-visual-interface.md)
+ Em Python com o Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Na linha de comandos com o Azure Machine Learning [extensão da CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Machine learning com uma área de trabalho

Tarefas de Machine learning de leitura e/ou artefactos de escrita para a área de trabalho. 

+ Executar uma experiência para preparar um modelo - escritas experimentar os resultados da execução para a área de trabalho.
+ Utilize automatizada ML para preparar um modelo - escreve resultados de treinamento para a área de trabalho.
+ Registe um modelo na área de trabalho.
+ Implantar um modelo - utiliza o modelo registado para criar uma implementação.
+ Criar e executar fluxos de trabalho reutilizáveis.
+ Vista aprendizagem artefactos, tais como experimentações, pipelines, modelos, implementações.
+ Modelos de controlar e monitorizar.

## <a name="workspace-management"></a>Gerenciamento de área de trabalho

Também pode efetuar as seguintes tarefas de gerenciamento de área de trabalho:

| Tarefa de gestão de área de trabalho   | Portal              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| Criar uma área de trabalho        | **&check;**     | **&check;** | **&check;** |
| Criar e gerir recursos de computação    | **&check;**   | **&check;** |  **&check;**   |
| Gerir o acesso de área de trabalho    | **&check;**   | |  **&check;**    |
| Criar uma VM do bloco de notas | **&check;**   | |     |

Introdução ao serviço pela [criar uma área de trabalho](setup-create-workspace.md).

## <a name="resources"></a> Recursos associados

Quando cria uma nova área de trabalho, este cria automaticamente vários recursos do Azure que são utilizados pela área de trabalho:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Regista os contentores do docker que utilizar durante o treinamento e ao implementar um modelo. Para minimizar os custos, é ACR **carregamento lento** até que sejam criadas imagens de implantação.
+ [Conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/): É utilizado como o arquivo de dados predefinido para a área de trabalho.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Arquivos de informações sobre os modelos de monitorização.
+ [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/): Os segredos de arquivos que são utilizados por computação destinos e outras informações confidenciais que é necessário pela área de trabalho.

> [!NOTE]
> Além de criar novas versões, também pode utilizar os serviços do Azure existentes.

## <a name="next-steps"></a>Passos Seguintes

Para começar a utilizar com o serviço Azure Machine Learning, veja:

+ [Descrição geral de serviço do Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Criar uma área de trabalho](setup-create-workspace.md)
+ [Gerir uma área de trabalho](how-to-manage-workspace.md)
+ [Tutorial: Preparar um modelo](tutorial-train-models-with-aml.md)
