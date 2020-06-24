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
ms.openlocfilehash: d870cb8d90573ab0c9661804aa1dd17b64d1600d
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84982633"
---
# <a name="enterprise-and-basic-editions-of-azure-machine-learning"></a>Empresa e Edições Básicas da Azure Machine Learning 

O Azure Machine Learning oferece duas edições adaptadas às suas necessidades de aprendizagem automática. Estas edições determinam quais as ferramentas de machine learning disponíveis para desenvolvedores e cientistas de dados a partir do seu espaço de trabalho.

<br/>
<br/>

| Edição básica | Edição empresarial                 |
|------------------------------------------------------------------------------------|-----------|
|Ótimo para: <br/>+ desenvolvimento de código aberto <br/>+ à escala de nuvens com um<br/>+ experiência de primeiro código <br/><br/>Os espaços básicos de trabalho permitem-lhe continuar a utilizar a Azure Machine Learning e [pagar apenas pelos recursos Azure consumidos](concept-plan-manage-cost.md) durante o processo ML. |Toda a edição básica, mais:<br/>+ a interface web do estúdio <br/>+ gestão segura e abrangente do ciclo de vida ML <br/>+ para todos os níveis de habilidade<br/><br/>Os espaços de trabalho da edição empresarial são cobrados apenas pelo seu consumo Azure enquanto a edição está em pré-visualização. |

## <a name="choose-an-edition"></a>Escolher uma edição

Atribui-se a edição sempre que cria um espaço de trabalho. Os clientes são responsáveis pelos custos incorridos no cálculo e outros recursos da Azure durante este período. Saiba como [gerir os custos para o Azure Machine Learning.](concept-plan-manage-cost.md)

Saiba como [atualizar um espaço de trabalho básico para a edição Enterprise.](how-to-manage-workspace.md#upgrade) 

## <a name="whats-in-each-edition"></a>O que há em cada edição

### <a name="data-for-machine-learning-capabilities"></a>Dados para capacidades de aprendizagem automática  

| Capacidades                     | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| Rotulagem: [Criar e gerir projetos de rotulagem](tutorial-labeling.md) em estúdio (Web)                                                | Todos                     |
| Rotulagem: Labeler em estúdio (Web)                                    | Todos                     |
| Rotulagem: Utilizar mão de obra privada                               | Todos                     |
| Rotulagem: [Classificação de imagem assistida ML e deteção de objetos](how-to-label-images.md)                  | Edição empresarial apenas |
| Datasets + datastores: criar e gerir em Python                       | Todos                     |
| Datasets + datastores: criar e gerir em estúdio (Web)                         | Todos                     |
| Drift: Ver e gerir monitores de conjuntos de dados em Python                           | Todos                     |
| Drift: Ver e gerir monitores de conjuntos de dados em estúdio (Web)                            | Edição empresarial apenas |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Capacidades de formação automatizadas (AutoML)

| Capacidades    | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| Criar e executar [experiências AutoML em cadernos](how-to-configure-auto-train.md)               | Todos                     |
| Criar e executar [experiências AutoML em estúdio (web)](how-to-use-automated-ml-for-ml-models.md)   | Edição empresarial apenas |
| Capacidades de previsão autoML líderes do setor             | Edição empresarial apenas |
| Apoio à aprendizagem profunda e a outros alunos avançados | Edição empresarial apenas |
| Grandes tarefas de classificação e regressão de dados (até 100 GB)                     | Edição empresarial apenas |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Aprendizagem de máquinas responsável

| Capacidades    | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Explicabilidade do modelo](how-to-machine-learning-interpretability-automl.md)                                              | Todos                     |
| [Privacidade diferencial](how-to-differential-privacy.md)                          | Todos                     |
| Etiquetas personalizadas para implementar folhas de dados    | Todos                     |
| Integração AzureML da Justiça                                      | Todos                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Construir e treinar capacidades

| Capacidades    | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| Integração visual do Código do Estúdio                                                     | Todos                     |
| Aprendizagem de Reforço                                                             | Todos                     |
| UI de experimentação                                                                 | Todos                     |
| Jupyter, Integração jupyterLab                                                    | Todos                     |
| Suporte Python SDK                                                                 | Todos                     |
| Suporte R SDK                                                                      | Todos                     |
| Oleodutos ML: Criar, executar e publicar em Python                           | Todos                     |
| Gasodutos ML: Criar, editar e apagar percursos programados de oleodutos em Python| Todos                     |
| Gasodutos ML: Criar pontos finais de gasodutos em Python SDK                                   | Todos                     |
| ML Pipelines: Ver detalhes de execução em estúdio (web)                                              | Todos                     |
| Oleodutos ML: Criar, executar, visualizar e publicar em designer                  | Edição empresarial apenas |
| Gasodutos ML: Criar pontos finais de gasodutos no designer | Edição empresarial apenas |
| Casos de computação geridos para cadernos integrados                                 | Todos                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Capacidades de implementação e gestão de modelos

| Capacidades                            | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| A extensão Azure DevOps para machine learning e o Azure ML CLI                 | Todos                     |
| [Integração do Event Grid](how-to-use-event-grid.md)                                                             | Todos                     |
| Integre a Azure Stream Analytics com Azure Machine Learning                       | Todos                     |
| Criar gasodutos ML em SDK                                                         | Todos                     |
| Inferenculação de lote                                                                  | Todos                     |
| Modelos acelerados de hardware baseados na FPGA                                             | Todos                     |
| Perfis de modelos                                                                    | Todos                     |
| Explicabilidade na UI                                                               | Edição empresarial apenas |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Capacidades de segurança, governação e controlo

| Capacidades     | Edição                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Suporte ao controlo de acesso baseado em](how-to-assign-roles.md) funções (RBAC)                                           | Todos                     |
| [Suporte de Rede Virtual (VNet)](how-to-enable-virtual-network.md) para cálculo                                         | Todos                     |
| Autenticação de ponto final de pontuação                                                    | Todos                     |
| [Link privado espaço de trabalho](how-to-configure-private-link.md)                                                            | Todos                     |
| [Gestão de quotas](how-to-manage-quotas.md) em espaços de trabalho                                                 | Edição empresarial apenas |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o que está disponível na página geral da edição Azure Machine Learning [e na página de preços.](https://azure.microsoft.com/pricing/details/machine-learning/) 

Saiba como [atualizar um espaço de trabalho básico para a edição Enterprise.](how-to-manage-workspace.md#upgrade) 
