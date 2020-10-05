---
title: O que é a Máquina Virtual Azure Data Science
titleSuffix: Azure Data Science Virtual Machine
description: Visão geral da Azure Data Science Virtual Machine - Uma máquina virtual fácil de usar na plataforma cloud Azure com ferramentas e bibliotecas pré-instaladas e configuradas para fazer ciência de dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 2bfcdfcb01e7908c199054e793d82cdfa1b726c7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88816342"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>O que é a Máquina Virtual Azure Data Science para Linux e Windows?

A Data Science Virtual Machine (DSVM) é uma imagem VM personalizada na plataforma cloud Azure construída especificamente para fazer ciência de dados. Tem muitas ferramentas populares de ciência de dados pré-instaladas e pré-configuradas para iniciar a construção de aplicações inteligentes para análise avançada.

O DSVM está disponível em:

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Comparação com Azure Machine Learning

O DSVM é uma imagem VM personalizada para a Data Science, mas [a Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) (AzureML) é uma plataforma de ponta a ponta que abrange:

+ Cálculo totalmente gerido
  + Instâncias de Computação
  + Clusters de cálculo para tarefas ML distribuídas
  + Inferência Clusters para pontuação em tempo real
+ Datastores (por exemplo Blob, ADLS Gen2, SQL DB)
+ Rastreio de experiências
+ Gestão de modelos
+ Notebooks
+ Ambientes (gerir dependências conda e R)
+ Rotulagem
+ Pipelines (automatizar fluxos de trabalho científicos de dados de ponta a ponta)

### <a name="comparison-with-azureml-compute-instances"></a>Comparação com AzureML Computação

[Azure Machine Learning Compute Instances](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance) é uma imagem VM totalmente configurada e __gerida,__ enquanto o DSVM é um VM __não gerido.__

As principais diferenças entre estas duas ofertas de produtos são detalhadas abaixo:


|Funcionalidade |Ciência dos Dados<br>VM |AzureML<br>Instância de Computação  | 
|---------|---------|---------|
| Totalmente gerido | Não        | Sim        |
|Suporte de Idiomas     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F #       | Python e R        |
|Sistema Operativo     | Ubuntu<br>Windows         |    Ubuntu     |
|Opção GPU pré-configurada     |  Sim       |    Sim     |
|Opção de escala | Sim | Sim |
|Acesso SSH    | Sim        |    Sim     |
|Acesso RDP    | Sim        |     Não    |
|Incorporado<br>Cadernos hospedados     |   Não<br>(requer uma configuração adicional)      |      Sim   |
|SSO embutido     | Não <br>(requer uma configuração adicional)         |    Sim     |
|Colaboração Incorporada     | Não         | Sim        |
|Ferramentas pré-instaladas     |  Jupyter (laboratório), RStudio Server, VSCode,<br> Estúdio Visual, PyCharm, Juno,<br>Power BI Desktop, SSMS, <br>Microsoft Office 365, Apache Drill       |     Jupyter(laboratório)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Exemplos de casos de utilização

Abaixo ilustramos alguns casos de uso comum para clientes DSVM.

### <a name="short-term-experimentation-and-evaluation"></a>Experimentação e avaliação a curto prazo

Você pode usar o DSVM para avaliar ou aprender novas ferramentas de ciência de [dados,](./tools-included.md)especialmente através de algumas das [nossas amostras publicadas e walkthroughs](./dsvm-samples-and-walkthroughs.md).

### <a name="deep-learning-with-gpus"></a>Aprendizagem profunda com GPUs

No DSVM, os seus modelos de treino podem usar algoritmos de aprendizagem profunda em hardware que é baseado em unidades de processamento de gráficos (GPUs). Ao tirar partido das capacidades de escala VM da plataforma Azure, o DSVM ajuda-o a utilizar hardware baseado em GPU na nuvem de acordo com as suas necessidades. Pode mudar para uma VM baseada em GPU quando estiver a preparar modelos grandes ou quando precisar de computações de alta velocidade, sem mudar o disco do SO. Pode escolher qualquer uma das GPUs da série N com a máquina virtual SKUs com DSVM. Note GPU ativado por máquinas virtuais SKUs não são suportados em contas gratuitas Azure.

As edições windows do DSVM vêm pré-instaladas com controladores de GPU, quadros e versões gpu de quadros de deep learning. Nas edições Linux, a aprendizagem profunda nas GPUs está ativada nos DSVMs Ubuntu. 

Também pode implementar as edições Ubuntu ou Windows do DSVM para uma máquina virtual Azure que não se baseia em GPUs. Neste caso, todos os quadros de aprendizagem profunda voltarão ao modo CPU.

[Saiba mais sobre a aprendizagem profunda disponível e os quadros de IA.](dsvm-tools-deep-learning-frameworks.md)

### <a name="data-science-training-and-education"></a>Formação e educação em ciência de dados

Os formadores e educadores empresariais que ensinam aulas de ciência de dados geralmente fornecem uma imagem de máquina virtual. A imagem garante que os alunos têm uma configuração consistente e que as amostras funcionam previsivelmente.

O DSVM cria um ambiente a pedido com uma configuração consistente que facilita os desafios de apoio e incompatibilidade. Os casos em que estes ambientes têm de ser criados constantemente, especialmente para aulas de formação mais pequenas, retiram benefícios significativos destas características.


## <a name="whats-included-on-the-dsvm"></a>O que está incluído no DSVM?

Consulte [aqui](tools-included.md)uma lista completa de ferramentas nos DSVMs Windows e Linux .

## <a name="next-steps"></a>Passos seguintes

Saiba mais com estes artigos:

+ Windows:
  + [Configurar uma DSVM do Windows](provision-vm.md)
  + [Ciência de dados num DSVM do Windows](vm-do-ten-things.md)

+ Linux:
  + [Configurar um Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Ciência de dados num Linux DSVM](linux-dsvm-walkthrough.md)
