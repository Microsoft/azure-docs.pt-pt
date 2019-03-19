---
title: O que aconteceu ao Machine Learning Workbench?
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre o que aconteceu com o Machine Learning Workbench, aplicativos, o que mudou no serviço Azure Machine Learning e o que é a linha cronológica de suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 01/09/2019
ms.custom: seodec18
ms.openlocfilehash: 5ff065816e287e0d4e37cdea9a7770c9ff806520
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890751"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>O que aconteceu ao Azure Machine Learning Workbench?

A aplicação Azure Machine Learning Workbench e alguns outros recursos antecipados foram preteridos e substituídos na versão de Setembro de 2018 para tornar a forma de uma melhor [arquitetura](concept-azure-machine-learning-architecture.md). 

Para melhorar a sua experiência, a versão contém várias atualizações significativas solicitadas pelo comentários dos clientes. A funcionalidade principal de execuções da experimentação para a implementação do modelo não mudou. Mas agora, pode usar a robusta <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> e o [CLI do Azure](reference-azure-machine-learning-cli.md) para realizar de aprendizagem, tarefas e pipelines.  

A maioria dos artefatos que foram criadas na versão anterior do serviço Azure Machine Learning é armazenada no seu próprio local ou de armazenamento na nuvem. Estes artefactos nunca irão desaparecer.

Neste artigo, ficará a conhecer o que foi alterado e como ele afeta o seu trabalho preexistente com o Azure Machine Learning Workbench e das respetivas APIs.

>[!Warning]
>Este artigo não é para os utilizadores do Azure Machine Learning Studio. É para os clientes do serviço do Azure Machine Learning que instalaram a aplicação Workbench (pré-visualização) e/ou tem experimentação e contas de pré-visualização de gestão de modelo.


## <a name="what-changed"></a>O que mudou?

A versão mais recente do serviço Azure Machine Learning inclui as seguintes funcionalidades:
+ R [modelo de recursos do Azure simplificada](concept-azure-machine-learning-architecture.md).
+ R [novo portal da interface do Usuário](how-to-track-experiments.md) para gerir as suas experimentações e destinos de computação.
+ Uma nova e mais abrangente de Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>.
+ O novo expandido [extensão de CLI do Azure](reference-azure-machine-learning-cli.md) para machine learning.

O [arquitetura](concept-azure-machine-learning-architecture.md) foi reestruturado para facilidade de utilização. Em vez de vários recursos e contas do Azure, precisa apenas de uma [Área de Trabalho do serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace). Pode criar áreas de trabalho rapidamente no [portal do Azure](quickstart-get-started.md). Ao utilizar uma área de trabalho, vários usuários podem armazenar a formação e implementação de computação destinos, experiências de modelo, imagens do Docker, modelos implementados e assim por diante.

Apesar de existirem novos clientes CLI e SDK aprimorados na versão atual, o próprio aplicativo da bancada de trabalho de área de trabalho ter sido extinguido. Experiências podem ser geridas do [dashboard da área de trabalho no portal do Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Utilize o dashboard para obter o histórico de experimentações, gerir os destinos de computação associados à sua área de trabalho, gerir os seus modelos e imagens do Docker e até implementar serviços Web.

<a name="timeline"></a>

## <a name="support-timeline"></a>Linha cronológica de suporte

9 de Janeiro de 2019 suporte para Machine Learning Workbench, experimentação do Azure Machine Learning e contas de gestão de modelos e respetivos SDK e CLI associados terminou. 

Todas as funcionalidades mais recentes estão disponíveis ao utilizar isto <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, o [CLI](reference-azure-machine-learning-cli.md)e o [portal](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>E em relação aos históricos de execução?

Históricos de execução mais antigos deixam de estar acessíveis, como ainda pode ver as execuções dos seus na versão mais recente. 

Históricos de execução são agora denominados **experimentações**. Pode recolher experimentações do seu modelo e explorá-los utilizando o SDK, a CLI ou o portal do Azure.

Dashboard de área de trabalho do portal é suportado nos browsers Microsoft Edge, Chrome e Firefox:

[![Online portal](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Inicie seus modelos de treinamento e ao controlar os históricos de execução usando a nova CLI e SDK. Pode saber como com o [Tutorial: criar modelos com o serviço Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="can-i-still-prep-data"></a>Ainda posso preparar os dados?

Os ficheiros de preparação de dados já existentes não são portáteis na versão mais recente porque não temos Machine Learning Workbench mais. Mas ainda pode preparar qualquer conjunto de dados de tamanho para modelagem.   

Com conjuntos de dados de qualquer tamanho, pode utilizar o [SDK do Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) para preparar rapidamente os seus dados antes da modelagem, escrevendo o código de Python. 

Pode seguir [deste tutorial](tutorial-data-prep.md) para saber mais sobre como utilizar o SDK do Azure Machine Learning Data Prep.

## <a name="will-projects-persist"></a>Os projetos serão mantidos?

Não irá perder qualquer código ou trabalho. Na versão mais antiga, os projetos são entidades na cloud com um diretório local. A versão mais recente, anexar diretórios locais para o área de trabalho de serviço do Azure Machine Learning, utilizando um ficheiro de configuração locais. Veja uma [diagrama da arquitetura do mais recente](concept-azure-machine-learning-architecture.md).

Grande parte do conteúdo do projeto já foi no seu computador local. Então, só precisa criar um ficheiro de configuração nesse diretório e referenciá-lo em seu código para ligar à sua área de trabalho. Para continuar a utilizar o diretório de local que contém os ficheiros e os scripts, especifique o nome do diretório no ['experiment.submit'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) comando do Python ou com o comando da CLI "anexar de projeto de ml az".  Por exemplo:
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

Saiba como começar a utilizar [Python com o SDK principal](quickstart-create-workspace-with-python.md) ou a utilizar [portal do Azure](quickstart-get-started.md).

## <a name="what-about-my-registered-models-and-images"></a>E o meu modelos registados e imagens?

Os modelos que registou no seu registo do modelo antigo devem ser migrados para nova área de trabalho, se quiser continuar a utilizá-los. Para migrar os seus modelos, transferir os modelos e voltar a registá-los na sua nova área de trabalho. 

As imagens que criou no seu registo de imagens antigo têm de ser recriadas na nova área de trabalho para continuar a utilizá-las. Pode recriar essas imagens ao seguir a [configurar e criar a imagem](how-to-deploy-and-where.md#configureimage) secções. 

## <a name="what-about-deployed-web-services"></a>E em relação aos serviços Web implementados?

Agora que o suporte para a CLI antiga terminou, já não pode voltar a implementar modelos ou gerir os serviços da web que originalmente implementado com a sua conta de gestão de modelos. No entanto, esses serviços web vão continuar a funcionar para, desde que o Azure Container Service (ACS) ainda é suportado.

Na versão mais recente, os modelos são implementados como serviços da web para clusters do Azure Container Instances (ACI) ou Azure Kubernetes Service (AKS). Também pode implementar para FPGAs e para o Azure IoT Edge. 

Saiba mais nestes artigos:
+ [Onde e como implementar modelos](how-to-deploy-and-where.md)
+ [Tutorial: Implementar modelos com o serviço Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="what-about-the-old-sdk-and-cli"></a>E quanto a SDK antigo e a CLI?

Sim, eles irá continuar a funcionar até Janeiro. Consulte precedente [linha cronológica](#timeline). Recomendamos que começar a criar os seus modelos e novas experimentações com o SDK mais recente ou a CLI.

Com o SDK de Python de novo na versão mais recente, pode interagir com o serviço Azure Machine Learning em qualquer ambiente de Python. Saiba como instalar a versão o <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> mais recente. Também pode utilizar o atualizada [extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md) com o conjunto avançado de `az ml` comandos para interagir com o serviço em qualquer ambiente de linha de comando, incluindo o Azure Cloud Shell.

## <a name="what-about-visual-studio-code-tools-for-ai"></a>E o Visual Studio Code Tools para IA?

Nesta versão mais recente, a extensão de nome foi mudada para Azure Machine Learning para Visual Studio Code e foi expandida e melhorada para funcionar com os novos recursos anteriores.

[![Azure Machine Learning para Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)](./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>E em relação aos pacotes de domínios?

Os pacotes de imagem digitalizada, análise de texto e previsão de domínio não podem ser utilizados com a versão mais recente do Azure Machine Learning. No entanto, ainda pode criar e preparar a imagem digitalizada, texto e modelos de previsão com o Azure Machine Learning Python mais recente <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>. Para saber como migrar os modelos pré-existentes criados usando-o de imagem digitalizada, análise de texto e previsão de pacotes, contacte [ AML-Packages@microsoft.com ](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [arquitetura da mais recente do serviço Azure Machine Learning](concept-azure-machine-learning-architecture.md). 

Para uma descrição geral do serviço, leia [o que é o serviço Azure Machine Learning?](overview-what-is-azure-ml.md)

Para obter um guia de introdução mostra-lhe como criar uma área de trabalho, crie um projeto, executar um script, e explore o histórico de execução do script com a versão mais recente do serviço Azure Machine Learning, experimente [introdução ao serviço Azure Machine Learning](quickstart-get-started.md).

Para uma experiência mais aprofundada para este fluxo de trabalho, siga os [tutorial completo](tutorial-train-models-with-aml.md) que contém os passos detalhados para formação e implementar modelos com o serviço Azure Machine Learning. 
