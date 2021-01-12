---
title: O que aconteceu ao Workbench?
titleSuffix: Azure Machine Learning
description: O Azure Machine Learning é uma solução integrada de ciência de dados para modelar e implementar aplicações ML à escala de nuvem. A função workbench foi reformada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: 812bbb2507310d088c42fa1cf2cad4c330755892
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072331"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>O que aconteceu ao Azure Machine Learning Workbench?

A aplicação Azure Machine Learning Workbench e algumas outras características iniciais foram depreciadas e substituídas no lançamento **de setembro de 2018** para dar lugar a uma [arquitetura](concept-azure-machine-learning-architecture.md)melhorada.

Para melhorar a sua experiência, o lançamento contém muitas atualizações significativas solicitadas pelo feedback do cliente. A funcionalidade principal da experiência vai até à implementação do modelo não mudou. Mas agora, você pode usar o robusto <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK,</a>R SDK, e o [Azure CLI](reference-azure-machine-learning-cli.md) para realizar suas tarefas de machine learning e pipelines.

A maioria dos artefactos que foram criados na versão anterior do Azure Machine Learning estão armazenados no seu próprio armazenamento local ou em nuvem. Estes artefactos nunca irão desaparecer.

Neste artigo, você aprende sobre o que mudou e como isso afeta o seu trabalho pré-existente com a Azure Machine Learning Workbench e as suas APIs.

>[!Warning]
>Este artigo não é para utilizadores do Azure Machine Learning Studio. Destina-se a clientes da Azure Machine Learning que instalaram a aplicação Workbench (pré-visualização) e/ou têm contas de experimentação e gestão de modelos.


## <a name="what-changed"></a>O que mudou?

O mais recente lançamento do Azure Machine Learning inclui as seguintes funcionalidades:
+ Um [modelo de recursos Azure simplificado.](concept-azure-machine-learning-architecture.md)
+ Um [novo portal UI](how-to-track-experiments.md) para gerir as suas experiências e metas de computação.
+ Um novo <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Escama SDK</a>Python mais abrangente.
+ A nova [extensão alargada do Azure CLI](reference-azure-machine-learning-cli.md) para machine learning.

A [arquitetura](concept-azure-machine-learning-architecture.md) foi redesenhada para facilitar o uso. Em vez de vários recursos e contas do Azure, precisa apenas de uma [Área de Trabalho do Azure Machine Learning](concept-workspace.md). Pode criar áreas de trabalho rapidamente no [portal do Azure](how-to-manage-workspace.md). Ao utilizar um espaço de trabalho, vários utilizadores podem armazenar alvos de treinamento e implementação de computação, experiências de modelos, imagens Docker, modelos implantados, e assim por diante.

Apesar de existirem novos clientes CLI e SDK melhorados na versão atual, a própria aplicação de workbench foi reformada. As experiências podem ser geridas no painel de instrumentos do [espaço de trabalho no estúdio Azure Machine Learning](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal). Utilize o dashboard para obter o histórico de experimentações, gerir os destinos de computação associados à sua área de trabalho, gerir os seus modelos e imagens do Docker e até implementar serviços Web.

<a name="timeline"></a>

## <a name="support-timeline"></a>Linha cronológica de suporte

No dia 9 de janeiro de 2019, o apoio à machine learning workbench, às contas de Experimentação e Gestão de Modelos da Azure Machine Learning, e as suas associadas SDK e CLI terminaram.

Todas as capacidades mais recentes estão disponíveis utilizando este <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK,</a>o [CLI](reference-azure-machine-learning-cli.md)e o [portal.](how-to-manage-workspace.md)

## <a name="what-about-run-histories"></a>E em relação aos históricos de execução?

As histórias de execução mais antigas já não são acessíveis, como ainda pode ver as suas execuções na versão mais recente.

As histórias de execução são agora chamadas **de experiências.** Pode recolher as experiências do seu modelo e explorá-las utilizando o SDK, o CLI ou o estúdio Azure Machine Learning.

O painel de trabalho do portal é suportado apenas nos navegadores Microsoft Edge, Chrome e Firefox:

[![Portal online](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Comece a treinar os seus modelos e rastreia as histórias de execução utilizando os novos CLI e SDK. Pode aprender como com o [Tutorial: treinar modelos com Azure Machine Learning.](tutorial-train-models-with-aml.md)

## <a name="will-projects-persist"></a>Os projetos serão mantidos?

Não irá perder qualquer código ou trabalho. Na versão mais antiga, os projetos são entidades na cloud com um diretório local. Na versão mais recente, você anexa diretórios locais ao espaço de trabalho Azure Machine Learning usando um ficheiro config local. Veja um [diagrama da arquitetura mais recente.](concept-azure-machine-learning-architecture.md)

Grande parte do conteúdo do projeto já estava na sua máquina local. Por isso, basta criar um ficheiro config nesse diretório e fazê-lo referenciar no seu código para se ligar ao seu espaço de trabalho. Para continuar a utilizar o diretório local que contém os seus ficheiros e scripts, especifique o nome do diretório no comando [Python 'experiment.submit'](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) ou utilizando o `az ml project attach` comando CLI.  Por exemplo:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Criar um espaço de trabalho](how-to-manage-workspace.md) para começar.

## <a name="what-about-my-registered-models-and-images"></a>E os meus modelos e imagens registados?

Os modelos que registou no seu antigo registo de modelos devem ser migrados para o seu novo espaço de trabalho se quiser continuar a utilizá-los. Para migrar os seus modelos, descarregue os modelos e registá-los novamente no seu novo espaço de trabalho.

As imagens que criou no seu antigo registo de imagem não podem ser diretamente migradas para o novo espaço de trabalho. Na maioria dos casos, o modelo pode ser implementado sem ter de criar uma imagem. Se necessário, pode criar uma imagem para o modelo no novo espaço de trabalho. Para obter mais informações, consulte [Gerir, registar, implementar e monitorizar modelos de machine learning](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>E em relação aos serviços Web implementados?

Agora que o suporte para o antigo CLI terminou, já não pode recolocar os modelos ou gerir os serviços web que foi originalmente implantado com a sua conta de Gestão de Modelos. No entanto, esses serviços web continuarão a funcionar enquanto o Serviço de Contentores Azure (ACS) continuar a ser suportado.

Na versão mais recente, os modelos são implementados como serviços web para clusters Azure Container Instances (ACI) ou Azure Kubernetes Service (AKS). Também pode ser implantado nas FPGAs e no Azure IoT Edge.

Saiba mais nestes artigos:
+ [Onde e como implementar modelos](how-to-deploy-and-where.md)
+ [Tutorial: Implementar modelos com aprendizagem automática Azure](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Passos seguintes

Conheça a [mais recente arquitetura para Azure Machine Learning.](concept-azure-machine-learning-architecture.md)

Para uma visão geral do serviço, leia [O que é Azure Machine Learning?](overview-what-is-azure-ml.md)

Crie a sua primeira experiência com o seu método preferido:

  + [Use o seu próprio ambiente](tutorial-1st-experiment-sdk-setup-local.md)
  + [Use cadernos Python](tutorial-1st-experiment-sdk-setup.md)
  + [Use R Markdown](tutorial-1st-r-experiment.md) 
  + [Utilize aprendizagem automática de máquinas](tutorial-designer-automobile-price-train-score.md) 
  + [Use as capacidades de arrastar & de arrasto do designer](tutorial-first-experiment-automated-ml.md) 
  + [Utilize a extensão ML ao CLI](tutorial-train-deploy-model-cli.md)
