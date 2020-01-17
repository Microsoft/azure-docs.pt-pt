---
title: O que é um espaço de trabalho
titleSuffix: Azure Machine Learning
description: O espaço de trabalho é o recurso de nível superior para Azure Machine Learning. Ele mantém um histórico de todas as execuções de treinamento, incluindo logs, métricas, saída e um instantâneo de seus scripts. Você usa essas informações para determinar qual execução de treinamento produz o melhor modelo
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: b2fb165dfdcc97116b60075a5c3071171fa46cd7
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157207"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>O que é um espaço de trabalho Azure Machine Learning?

O espaço de trabalho é o recurso de nível superior para Azure Machine Learning, fornecendo um local centralizado para trabalhar com todos os artefatos que você criar ao usar Azure Machine Learning.  O espaço de trabalho mantém um histórico de todas as execuções de treinamento, incluindo logs, métricas, saída e um instantâneo de seus scripts. Use essas informações para determinar qual execução de treinamento produz o melhor modelo.  

Quando você tiver um modelo que desejar, registre-o no espaço de trabalho. Em seguida, você usa o modelo registrado e os scripts de Pontuação para implantar nas instâncias de contêiner do Azure, no serviço kubernetes do Azure ou em uma FPGA (matriz de porta programável por campo) como um ponto de extremidade HTTP baseado em REST. Você também pode implantar o modelo em um dispositivo Azure IoT Edge como um módulo.

Os preços e os recursos disponíveis dependem de se a [edição Basic ou Enterprise](overview-what-is-azure-ml.md#sku) está selecionada para o espaço de trabalho. Você seleciona a edição ao [criar o espaço de trabalho](#create-workspace).  Você também pode [Atualizar](#upgrade) do Basic para a Enterprise Edition.

## <a name="taxonomy"></a>Taxonomia 

Uma taxonomia do espaço de trabalho é ilustrada no diagrama a seguir:

[![Taxonomia da área de trabalho](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

O diagrama mostra os seguintes componentes de um espaço de trabalho:

+ Um espaço de trabalho pode conter [Azure Machine Learning instâncias de computação](concept-compute-instance.md), recursos de nuvem configurados com o ambiente Python necessário para executar Azure Machine Learning.

+ As [funções de usuário](how-to-assign-roles.md) permitem que você compartilhe seu espaço de trabalho com outros usuários, equipes ou projetos.
+ Os [destinos de computação](concept-azure-machine-learning-architecture.md#compute-targets) são usados para executar seus experimentos.
+ Quando você cria o espaço de trabalho, os [recursos associados](#resources) também são criados para você.
+ Os [experimentos](concept-azure-machine-learning-architecture.md#experiments) são as execuções de treinamento que você usa para criar seus modelos.  
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) são fluxos de trabalho reutilizáveis para treinar e treinar novamente seu modelo.
+ [DataSets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) auxiliam no gerenciamento dos dados que você usa para treinamento de modelo e criação de pipeline.
+ Quando você tiver um modelo que deseja implantar, crie um modelo registrado.
+ Use o modelo registrado e um script de Pontuação para criar um [ponto de extremidade de implantação](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Ferramentas para interação do espaço de trabalho

Você pode interagir com seu espaço de trabalho das seguintes maneiras:

+ Na Web:
    + [Azure Machine Learning Studio](https://ml.azure.com) 
    + [Azure Machine Learning designer (versão prévia)](concept-designer.md) – disponível somente em espaços de trabalho do [Enterprise Edition](overview-what-is-azure-ml.md#sku) .
+ Em qualquer ambiente do Python com o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Em qualquer ambiente de R com o [SDK do Azure Machine Learning para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Na linha de comando usando a [extensão da CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) do Azure Machine Learning

## <a name="machine-learning-with-a-workspace"></a>Aprendizado de máquina com um espaço de trabalho

As tarefas de aprendizado de máquina lêem e/ou gravam artefatos em seu espaço de trabalho.

+ Executar um experimento para treinar um modelo – grava os resultados da execução do experimento no espaço de trabalho.
+ Use o ML automatizado para treinar um modelo – grava os resultados de treinamento no espaço de trabalho.
+ Registrar um modelo no espaço de trabalho.
+ Implantar um modelo – usa o modelo registrado para criar uma implantação.
+ Crie e execute fluxos de trabalho reutilizáveis.
+ Exiba artefatos de Machine Learning, como experimentos, pipelines, modelos, implantações.
+ Acompanhe e monitore modelos.

## <a name="workspace-management"></a>Gerenciamento de espaço de trabalho

Você também pode executar as seguintes tarefas de gerenciamento de espaço de trabalho:

| Tarefa de gerenciamento de espaço de trabalho   | Portal              | Studio | SDK/R do SDK do Python       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Criar áreas de trabalho        | **&check;**     | | **&check;** | **&check;** |
| Gerenciar o acesso ao espaço de trabalho    | **&check;**   || |  **&check;**    |
| Atualizar para o Enterprise Edition    | **&check;** | **&check;**  | |     |
| Criar e gerenciar recursos de computação    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Criar uma VM do notebook |   | **&check;** | |     |

> [!NOTE]
> As instâncias de computação estão disponíveis somente para espaços de trabalho com uma região de **EUA Central norte** ou **sul do Reino Unido**.
>Se o seu espaço de trabalho estiver em qualquer outra região, você poderá continuar a criar e usar uma [VM do bloco de anotações](concept-compute-instance.md#notebookvm) .

## <a name='create-workspace'></a>Criar um espaço de trabalho

Ao criar um espaço de trabalho, você decide se deseja criá-lo com o [Basic ou Enterprise Edition](overview-what-is-azure-ml.md#sku). A edição determina os recursos disponíveis no espaço de trabalho. Entre outros recursos, a Enterprise Edition oferece acesso ao [Azure Machine Learning designer](concept-designer.md) e à versão de estúdio da criação de [experimentos de aprendizado de máquina automatizados](tutorial-first-experiment-automated-ml.md).  Para obter mais detalhes e informações sobre preços, consulte [preços de Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Há várias maneiras de criar um espaço de trabalho:  

* Use o [portal do Azure](how-to-manage-workspace.md) para uma interface de apontar e clicar para orientá-lo em cada etapa.
* Use o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) para criar um espaço de trabalho instantaneamente de scripts Python ou notebooks Júpiter
* Use um [modelo de Azure Resource Manager](how-to-create-workspace-template.md) ou a [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md) quando precisar automatizar ou personalizar a criação com os padrões de segurança corporativa.
* Se você trabalhar em Visual Studio Code, use a [extensão vs Code](tutorial-setup-vscode-extension.md).

## <a name="upgrade"></a>Atualizar para o Enterprise Edition

Você pode [atualizar seu espaço de trabalho de básico para Enterprise Edition](how-to-manage-workspace.md#upgrade) usando o portal do Azure. Não é possível fazer downgrade de um espaço de trabalho Enterprise Edition para um espaço de trabalho básico. 

## <a name="resources"></a>Recursos associados

Quando cria uma nova área de trabalho, este cria automaticamente vários recursos do Azure que são utilizados pela área de trabalho:

+ [Registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/): registra os contêineres do Docker que você usa durante o treinamento e quando implanta um modelo. Para minimizar os custos, o ACR é **carregado com o preguiçoso** até que as imagens de implantação sejam criadas.
+ [Conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/): é usada como o repositório de armazenamento padrão para o espaço de trabalho.  Os blocos de anotações do Jupyter que são usados com suas instâncias de computação Azure Machine Learning também são armazenados aqui.
+ [Insights de aplicativo Azure](https://azure.microsoft.com/services/application-insights/): armazena informações de monitoramento sobre seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): armazena segredos que são usados por destinos de computação e outras informações confidenciais que são necessárias para o espaço de trabalho.

> [!NOTE]
> Além de criar novas versões, você também pode usar os serviços existentes do Azure.

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o Azure Machine Learning, consulte:

+ [Visão geral de Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Criar uma área de trabalho](how-to-manage-workspace.md)
+ [Gerir uma área de trabalho](how-to-manage-workspace.md)
+ [Tutorial: introdução à criação de seu primeiro experimento do ML com o SDK do Python](tutorial-1st-experiment-sdk-setup.md)
+ [Tutorial: introdução ao Azure Machine Learning com o SDK do R](tutorial-1st-r-experiment.md)
+ [Tutorial: criar seu primeiro modelo de classificação com o Machine Learning automatizado](tutorial-first-experiment-automated-ml.md) (disponível somente em espaços de trabalho do [Enterprise Edition](overview-what-is-azure-ml.md#sku) )
+ [Tutorial: prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md) (disponível somente em espaços de trabalho do [Enterprise Edition](overview-what-is-azure-ml.md#sku) )
