---
title: O que aconteceu ao Workbench?
titleSuffix: Azure Machine Learning
description: Saiba o que é possível sem a aplicação workbench e qual é a linha temporal de suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 03/05/2020
ms.openlocfilehash: 5a8d31157023cf75076bfef7df09e65a066ceaa5
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328625"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>O que aconteceu ao Azure Machine Learning Workbench?

A aplicação azure machine learning workbench e algumas outras funcionalidades iniciais foram depreciadas e substituídas no lançamento de setembro de **2018** para dar lugar a uma [arquitetura](concept-azure-machine-learning-architecture.md)melhorada.

Para melhorar a sua experiência, o lançamento contém muitas atualizações significativas solicitadas pelo feedback do cliente. A funcionalidade principal desde as experiências até à implementação do modelo não mudou. Mas agora, você pode usar o robusto <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK,</a>R SDK e o [Azure CLI](reference-azure-machine-learning-cli.md) para realizar suas tarefas e oleodutos de aprendizagem automática.

A maioria dos artefactos que foram criados na versão anterior do Azure Machine Learning são armazenados no seu próprio armazenamento local ou em nuvem. Estes artefactos nunca irão desaparecer.

Neste artigo, você aprende sobre o que mudou e como isso afeta o seu trabalho pré-existente com a bancada de trabalho de aprendizagem automática Azure e suas APIs.

>[!Warning]
>Este artigo não é para os utilizadores do Azure Machine Learning Studio. Destina-se aos clientes da Azure Machine Learning que instalaram a aplicação Workbench (pré-visualização) e/ou têm contas de experimentação e gestão de modelos.


## <a name="what-changed"></a>O que mudou?

O mais recente lançamento do Azure Machine Learning inclui as seguintes funcionalidades:
+ Um [modelo de recursos Azure simplificado.](concept-azure-machine-learning-architecture.md)
+ Um [novo portal UI](how-to-track-experiments.md) para gerir as suas experiências e calcular alvos.
+ Um novo E mais abrangente <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK.</a>
+ A nova [extensão azure CLI](reference-azure-machine-learning-cli.md) expandida para aprendizagem automática.

A [arquitetura](concept-azure-machine-learning-architecture.md) foi redesenhada para facilitar o uso. Em vez de vários recursos e contas do Azure, precisa apenas de uma [Área de Trabalho do Azure Machine Learning](concept-workspace.md). Pode criar áreas de trabalho rapidamente no [portal do Azure](how-to-manage-workspace.md). Ao utilizar um espaço de trabalho, vários utilizadores podem armazenar alvos de computação de treinamento e implementação, experiências de modelos, imagens do Docker, modelos implantados, e assim por diante.

Apesar de existirem novos clientes CLI e SDK melhorados no lançamento atual, a própria aplicação de bancada de desktop foi reformada. As experiências podem ser geridas no painel de instrumentos do espaço de [trabalho no estúdio Azure Machine Learning.](how-to-track-experiments.md#view-the-experiment-in-the-web-portal) Utilize o dashboard para obter o histórico de experimentações, gerir os destinos de computação associados à sua área de trabalho, gerir os seus modelos e imagens do Docker e até implementar serviços Web.

<a name="timeline"></a>

## <a name="support-timeline"></a>Linha cronológica de suporte

No dia 9 de janeiro de 2019 terminou o apoio à bancada de trabalho de machine learning, às contas de Experimentação de Machine Learning e Model Management, e terminou o seu SDK e CLI associados.

Todas as mais recentes capacidades estão disponíveis utilizando este <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK,</a>o [CLI,](reference-azure-machine-learning-cli.md)e o [portal](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>E em relação aos históricos de execução?

As histórias de corridamais antigas já não são acessíveis, como ainda pode ver as suas corridas na versão mais recente.

As histórias de execução são agora chamadas **de experiências.** Pode recolher as experiências do seu modelo e explorá-las utilizando o SDK, o CLI ou o estúdio Azure Machine Learning.

O painel de instrumentos do espaço de trabalho do portal é suportado apenas nos navegadores Microsoft Edge, Chrome e Firefox:

[portal online ![](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Inicie seus modelos de treinamento e ao controlar os históricos de execução usando a nova CLI e SDK. Pode aprender com o [Tutorial: treine modelos com Azure Machine Learning.](tutorial-train-models-with-aml.md)

## <a name="will-projects-persist"></a>Os projetos serão mantidos?

Não irá perder qualquer código ou trabalho. Na versão mais antiga, os projetos são entidades na cloud com um diretório local. Na versão mais recente, anexa os diretórios locais ao espaço de trabalho azure machine learning utilizando um ficheiro config local. Veja um [diagrama da arquitetura mais recente.](concept-azure-machine-learning-architecture.md)

Grande parte do conteúdo do projeto já estava na sua máquina local. Por isso, basta criar um ficheiro config nesse diretório e remectá-lo no seu código para se ligar ao seu espaço de trabalho. Para continuar a utilizar o diretório local que contém os seus ficheiros e scripts, especifique o nome do diretório no comando [Python 'experiment.submit'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) ou utilizando o comando `az ml project attach` CLI.  Por exemplo:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Criar um espaço de trabalho](how-to-manage-workspace.md) para começar.

## <a name="what-about-my-registered-models-and-images"></a>E os meus modelos e imagens registados?

Os modelos que registou no seu registo do modelo antigo devem ser migrados para nova área de trabalho, se quiser continuar a utilizá-los. Para migrar os seus modelos, descarregue os modelos e registe-os novamente no seu novo espaço de trabalho.

As imagens que criou no seu antigo registo de imagem não podem ser diretamente migradas para o novo espaço de trabalho. Na maioria dos casos, o modelo pode ser implantado sem ter de criar uma imagem. Se necessário, pode criar uma imagem para o modelo no novo espaço de trabalho. Para mais informações, consulte [Gerir, registar, implementar e monitorizar modelos de aprendizagem automática.](concept-model-management-and-deployment.md)

## <a name="what-about-deployed-web-services"></a>E em relação aos serviços Web implementados?

Agora que o suporte para o antigo CLI terminou, já não pode reimplantar modelos ou gerir os serviços web que implementou originalmente com a sua conta de Gestão de Modelos. No entanto, esses serviços web vão continuar a funcionar para, desde que o Azure Container Service (ACS) ainda é suportado.

Na versão mais recente, os modelos são implementados como serviços da web para clusters do Azure Container Instances (ACI) ou Azure Kubernetes Service (AKS). Também pode ser implantado para FPGAs e Para Azure IoT Edge.

Saiba mais nestes artigos:
+ [Onde e como implementar modelos](how-to-deploy-and-where.md)
+ [Tutorial: Implementar modelos com Aprendizagem automática Azure](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Passos seguintes

Conheça a [mais recente arquitetura para Azure Machine Learning.](concept-azure-machine-learning-architecture.md)

Para uma visão geral do serviço, leia [o que é azure machine learning?](overview-what-is-azure-ml.md)

Crie a sua primeira experiência com o seu método preferido:
  + [Use cadernos Python](tutorial-1st-experiment-sdk-setup.md)
  + [Utilizar R Markdown](tutorial-1st-r-experiment.md) 
  + [Utilize aprendizagem automática de máquinas](tutorial-designer-automobile-price-train-score.md) 
  + [Use as capacidades de arrastar e largar do designer](tutorial-first-experiment-automated-ml.md) 
  + [Utilize a extensão ML do CLI](tutorial-train-deploy-model-cli.md)