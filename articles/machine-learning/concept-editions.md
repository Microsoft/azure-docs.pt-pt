---
title: Azure Machine Learning Enterprise e Edições Básicas
description: Conheça as diferenças entre as edições do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: aa754868677802b7d0000045f22090fbca62d9b6
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927424"
---
# <a name="enterprise-and-basic-editions-of-azure-machine-learning"></a>Empresa e Edições Básicas da Azure Machine Learning 

O Azure Machine Learning oferece duas edições adaptadas às suas necessidades de aprendizagem automática. Estas edições determinam quais as ferramentas de machine learning disponíveis para desenvolvedores e cientistas de dados a partir do seu espaço de trabalho.

## <a name="choose-an-edition"></a>Escolher uma edição

Atribui-se a edição sempre que cria um espaço de trabalho. Os clientes são responsáveis pelos custos incorridos no cálculo e outros recursos da Azure durante este período. Saiba como [gerir os custos para o Azure Machine Learning.](concept-plan-manage-cost.md)

Saiba como [atualizar um espaço de trabalho básico para a edição Enterprise.](how-to-manage-workspace.md#upgrade) 

## <a name="whats-in-each-edition"></a>O que há em cada edição

### <a name="data-for-machine-learning-capabilities"></a>Dados para capacidades de aprendizagem automática  

| Capacidades                     | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| Rotulagem: [Criar e gerir projetos de rotulagem](tutorial-labeling.md) em estúdio (Web)                                                | Tudo                     |
| Rotulagem: Labeler em estúdio (Web)                                    | Tudo                     |
| Rotulagem: Utilizar mão de obra privada                               | Tudo                     |
| Rotulagem: [Classificação de imagem assistida ML e deteção de objetos](how-to-label-images.md)                  | Edição empresarial apenas |
| Datasets + datastores: criar e gerir em Python                       | Tudo                     |
| Datasets + datastores: criar e gerir em estúdio (Web)                         | Tudo                     |
| Drift: Ver e gerir monitores de conjuntos de dados em Python                           | Tudo                     |
| Drift: Ver e gerir monitores de conjuntos de dados em estúdio (Web)                            | Edição empresarial apenas |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Capacidades de formação automatizadas (AutoML)

| Capacidades    | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| Criar e executar [experiências AutoML em cadernos](how-to-configure-auto-train.md)               | Tudo                     |
| Criar e executar [experiências AutoML em estúdio (web)](how-to-use-automated-ml-for-ml-models.md)   | Edição empresarial apenas |
| Capacidades de previsão autoML líderes do setor             | Edição empresarial apenas |
| Apoio à aprendizagem profunda e a outros alunos avançados | Edição empresarial apenas |
| Grandes tarefas de classificação e regressão de dados (até 100 GB)                     | Edição empresarial apenas |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Aprendizagem de máquinas responsável

| Capacidades    | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Explicabilidade do modelo](how-to-machine-learning-interpretability-automl.md)                                              | Tudo                     |
| [Privacidade diferencial](how-to-differential-privacy.md)                          | Tudo                     |
| Etiquetas personalizadas para implementar folhas de dados    | Tudo                     |
| Integração AzureML da Justiça                                      | Tudo                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Construir e treinar capacidades

| Capacidades    | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| Integração visual do Código do Estúdio                                                     | Tudo                     |
| Aprendizagem de Reforço                                                             | Tudo                     |
| UI de experimentação                                                                 | Tudo                     |
| Jupyter, Integração jupyterLab                                                    | Tudo                     |
| Suporte Python SDK                                                                 | Tudo                     |
| Suporte R SDK                                                                      | Tudo                     |
| Oleodutos ML: Criar, executar e publicar em Python                           | Tudo                     |
| Gasodutos ML: Criar, editar e apagar percursos programados de oleodutos em Python| Tudo                     |
| Gasodutos ML: Criar pontos finais de gasodutos em Python SDK                                   | Tudo                     |
| ML Pipelines: Ver detalhes de execução em estúdio (web)                                              | Tudo                     |
| Oleodutos ML: Criar, executar, visualizar e publicar em designer                  | Edição empresarial apenas |
| Gasodutos ML: Criar pontos finais de gasodutos no designer | Edição empresarial apenas |
| Casos de computação geridos para cadernos integrados                                 | Tudo                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Capacidades de implementação e gestão de modelos

| Capacidades                            | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| A extensão Azure DevOps para machine learning e o Azure ML CLI                 | Tudo                     |
| [Integração do Event Grid](how-to-use-event-grid.md)                                                             | Tudo                     |
| Integre a Azure Stream Analytics com Azure Machine Learning                       | Tudo                     |
| Criar gasodutos ML em SDK                                                         | Tudo                     |
| Inferenculação de lote                                                                  | Tudo                     |
| Modelos acelerados de hardware baseados na FPGA                                             | Tudo                     |
| Perfis de modelos                                                                    | Tudo                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Capacidades de segurança, governação e controlo

| Capacidades     | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Suporte ao controlo de acesso baseado em funções Azure (Azure RBAC)](how-to-assign-roles.md)                                           | Tudo                     |
| [Suporte de Rede Virtual (VNet)](how-to-enable-virtual-network.md) para cálculo                                         | Tudo                     |
| Autenticação de ponto final de pontuação                                                    | Tudo                     |
| [Link privado espaço de trabalho](how-to-configure-private-link.md)                                                            | Tudo                     |
| [Gestão de quotas](how-to-manage-quotas.md) em espaços de trabalho                                                 | Edição empresarial apenas |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o que está disponível na página geral da edição Azure Machine Learning [e na página de preços.](https://azure.microsoft.com/pricing/details/machine-learning/) 

Saiba como [atualizar um espaço de trabalho básico para a edição Enterprise.](how-to-manage-workspace.md#upgrade) 
