---
title: O que aconteceu ao Workbench?
titleSuffix: Azure Machine Learning
description: Saiba mais sobre o que é possível sem o aplicativo Workbench e qual é a linha do tempo de suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 11/05/2019
ms.custom: seodec18
ms.openlocfilehash: 835024f7c8d77d67a2f6321df658c2f5c2007e62
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647110"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>O que aconteceu ao Azure Machine Learning Workbench?

O aplicativo Azure Machine Learning Workbench e alguns recursos iniciais foram preteridos e substituídos na versão de **setembro de 2018** para ter uma [arquitetura](concept-azure-machine-learning-architecture.md)aprimorada.

Para melhorar sua experiência, a versão contém muitas atualizações significativas solicitadas pelos comentários dos clientes. A funcionalidade básica do experimento é executada para modelar a implantação não mudou. Mas agora, você pode usar o <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK do Python</a>robusto, o SDK do R e o [CLI do Azure](reference-azure-machine-learning-cli.md) para realizar suas tarefas e pipelines de aprendizado de máquina.

A maioria dos artefatos criados na versão anterior do Azure Machine Learning são armazenados em seu próprio armazenamento local ou na nuvem. Estes artefactos nunca irão desaparecer.

Neste artigo, você aprende sobre o que mudou e como ele afeta o trabalho pré-existente com o Azure Machine Learning Workbench e suas APIs.

>[!Warning]
>Este artigo não é para Azure Machine Learning Studio usuários. É para Azure Machine Learning clientes que instalaram o aplicativo Workbench (versão prévia) e/ou têm contas de versão prévia de gerenciamento de modelos e experimentação.


## <a name="what-changed"></a>O que mudou?

A versão mais recente do Azure Machine Learning inclui os seguintes recursos:
+ Um [modelo simplificado de recursos do Azure](concept-azure-machine-learning-architecture.md).
+ Uma [nova interface do usuário do portal](how-to-track-experiments.md) para gerenciar seus experimentos e destinos de computação.
+ Um <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>do Python novo e mais abrangente.
+ A nova [extensão de CLI do Azure](reference-azure-machine-learning-cli.md) expandida para aprendizado de máquina.

A [arquitetura](concept-azure-machine-learning-architecture.md) foi reprojetada para facilitar o uso. Em vez de vários recursos e contas do Azure, precisa apenas de uma [Área de Trabalho do Azure Machine Learning](concept-workspace.md). Pode criar áreas de trabalho rapidamente no [portal do Azure](how-to-manage-workspace.md). Usando um espaço de trabalho, vários usuários podem armazenar destinos de computação de treinamento e implantação, experimentos de modelo, imagens do Docker, modelos implantados e assim por diante.

Embora haja novos clientes de CLI e SDK aprimorados na versão atual, o próprio aplicativo do desktop Workbench foi desativado. Os experimentos podem ser gerenciados no [painel do espaço de trabalho no Azure Machine Learning Studio](how-to-track-experiments.md#view-the-experiment-in-the-web-portal). Utilize o dashboard para obter o histórico de experimentações, gerir os destinos de computação associados à sua área de trabalho, gerir os seus modelos e imagens do Docker e até implementar serviços Web.

<a name="timeline"></a>

## <a name="support-timeline"></a>Linha cronológica de suporte

Em 9 de Janeiro, 2019 suporte para Machine Learning Workbench, Experimentação do Azure Machine Learning e contas de Gerenciamento de Modelos, e o SDK e a CLI associados foram encerrados.

Todos os recursos mais recentes estão disponíveis usando esse <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>, a [CLI](reference-azure-machine-learning-cli.md)e o [portal](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>E em relação aos históricos de execução?

Os históricos de execução mais antigos não são mais acessíveis, como você ainda pode ver suas execuções na versão mais recente.

Os históricos de execução agora são chamados de **experimentos**. Você pode coletar os experimentos do modelo e explorá-los usando o SDK, a CLI ou o Azure Machine Learning Studio.

O painel de espaço de trabalho do portal tem suporte apenas nos navegadores Microsoft Edge, Chrome e Firefox:

[![portal online](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Comece a treinar seus modelos e rastreie os históricos de execução usando a nova CLI e o SDK. Você pode aprender como com o [tutorial: treinar modelos com Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Os projetos serão mantidos?

Não irá perder qualquer código ou trabalho. Na versão mais antiga, os projetos são entidades na cloud com um diretório local. Na versão mais recente, anexe diretórios locais ao espaço de trabalho Azure Machine Learning usando um arquivo de configuração local. Veja um [diagrama da arquitetura mais recente](concept-azure-machine-learning-architecture.md).

Grande parte do conteúdo do projeto já estava em seu computador local. Portanto, você só precisa criar um arquivo de configuração nesse diretório e referenciá-lo em seu código para se conectar ao seu espaço de trabalho. Para continuar usando o diretório local que contém seus arquivos e scripts, especifique o nome do diretório no comando Python [' experimento. Submit '](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) ou usando o comando `az ml project attach` CLI.  Por exemplo:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Crie um espaço de trabalho](how-to-manage-workspace.md) para começar.

## <a name="what-about-my-registered-models-and-images"></a>E quanto aos meus modelos e imagens registrados?

Os modelos que você registrou em seu registro de modelo antigo devem ser migrados para seu novo espaço de trabalho se você quiser continuar a usá-los. Para migrar seus modelos, baixe os modelos e registre-os novamente em seu novo espaço de trabalho.

As imagens que você criou em seu registro de imagem antiga não podem ser migradas diretamente para o novo espaço de trabalho. Na maioria dos casos, o modelo pode ser implantado sem a necessidade de criar uma imagem. Se necessário, você pode criar uma imagem para o modelo no novo espaço de trabalho. Para obter mais informações, consulte [gerenciar, registrar, implantar e monitorar modelos de aprendizado de máquina](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>E em relação aos serviços Web implementados?

Agora que o suporte para a CLI antiga terminou, você não pode mais reimplantar modelos ou gerenciar os serviços Web que você implantou originalmente com sua conta de Gerenciamento de Modelos. No entanto, esses serviços Web continuarão a funcionar enquanto o ACS (serviço de contêiner do Azure) ainda tiver suporte.

Na versão mais recente, os modelos são implantados como serviços Web para os clusters ACI (instâncias de contêiner do Azure) ou AKS (serviço kubernetes do Azure). Você também pode implantar o FPGAs e para Azure IoT Edge.

Saiba mais nestes artigos:
+ [Onde e como implantar modelos](how-to-deploy-and-where.md)
+ [Tutorial: implantar modelos com Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [arquitetura mais recente para Azure Machine Learning](concept-azure-machine-learning-architecture.md).

Para obter uma visão geral do serviço, leia [o que é Azure Machine Learning?](overview-what-is-azure-ml.md).

Crie seu primeiro experimento com o método preferencial:
  + [Usar blocos de anotações do Python para treinar & implantar modelos de ML](tutorial-1st-experiment-sdk-setup.md)
  + [Usar R Markdown para treinar & implantar modelos de ML]( tutorial-1st-r-experiment.md) 
  + [Usar o Machine Learning automatizado para treinar & implantar modelos de ML](ui-tutorial-automobile-price-train-score.md) 
  + [Use os recursos de arrastar & do designer para treinar & implantar](tutorial-first-experiment-automated-ml.md) 
  + [Usar a CLI do Machine Learning para treinar e implantar um modelo](tutorial-train-deploy-model-cli.md)
