---
title: Ferramentas de ciência de dados e aprendizado de máquina
description: Saiba mais sobre as ferramentas e as estruturas de aprendizado de máquina que são pré-instalados no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 8eae1501973d24dc468056229e8a762910f2f181
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330760"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Ferramentas de ciência de dados e aprendizado de máquina em máquinas virtuais de ciência de dados do Azure
As DSVMs (máquinas virtuais de ciência de dados) do Azure têm um conjunto avançado de ferramentas e bibliotecas para o aprendizado de máquina disponível em linguagens populares, como Python, R e Julia.

Aqui estão algumas das ferramentas e bibliotecas de aprendizado de máquina no DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>SDK do Azure Machine Learning para Python

Consulte a referência completa para o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| O que é?   |   Azure Machine Learning é um serviço de nuvem que você pode usar para desenvolver e implantar modelos de aprendizado de máquina. Você pode acompanhar seus modelos ao criar, treinar, dimensionar e gerenciá-los usando o SDK do Python. Implante modelos como contêineres e execute-os na nuvem, no local ou em Azure IoT Edge.   |
| Edições com suporte     | Windows (ambiente Conda: AzureML), Linux (ambiente Conda: py36)    |
| Usos típicos      | Plataforma de aprendizado de máquina geral      |
| Como ele é configurado ou instalado?      |  Instalado com suporte de GPU   |
| Como usá-lo ou executá-lo      | Como um SDK do Python e no CLI do Azure. Ative para o ambiente Conda `AzureML` no Windows Edition *ou* para `py36` na edição Linux.      |
| Link para amostras      | Os notebooks Jupyter de exemplo estão incluídos no diretório `AzureML` em notebooks.  |
| Ferramentas relacionadas      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| O que é?   | Uma plataforma de ia de software livre que dá suporte ao aprendizado de máquina na memória, distribuída, rápida e escalonável.  |
| Versões suportadas      | Linux   |
| Usos típicos      | Aprendizado de máquina distribuído e escalonável de uso geral   |
| Como ele é configurado ou instalado?      | O H2O é instalado em `/dsvm/tools/h2o`.      |
| Como usá-lo ou executá-lo      | Conecte-se à VM usando X2Go. Inicie um novo terminal e execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Em seguida, inicie um navegador da Web e conecte-se a `http://localhost:54321`.      |
| Link para amostras      | Os exemplos estão disponíveis na VM em Jupyter no diretório `h2o`.      |
| Ferramentas relacionadas      | Apache Spark, MXNet, XGBoost, água de despertar, água profunda    |

Há várias outras bibliotecas de aprendizado de máquina em DSVMs, como o popular pacote `scikit-learn` que faz parte da distribuição Anaconda Python para DSVMs. Para conferir a lista de pacotes disponíveis em Python, R e Julia, execute os respectivos gerenciadores de pacotes.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| O que é?   | Uma estrutura de reforço de gradiente rápida, distribuída e de alto desempenho (GBDT, GBRT, GBM ou MART) com base em algoritmos de árvore de decisão. Ele é usado para classificação, classificação e muitas outras tarefas de aprendizado de máquina.    |
| Versões suportadas      | Windows, Linux    |
| Usos típicos      | Estrutura de aumento de gradiente de uso geral      |
| Como ele é configurado ou instalado?      | No Windows, o LightGBM é instalado como um pacote do Python. No Linux, o executável de linha de comando está em `/opt/LightGBM/lightgbm`, o pacote R está instalado e os pacotes do Python estão instalados.     |
| Link para amostras      | [Guia do LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma interface gráfica do usuário para Data Mining usando o R.   |
| Edições com suporte     | Windows, Linux     |
| Usos típicos      | Ferramenta de mineração de dados de interface do usuário geral para R    |
| Como usá-lo ou executá-lo      | Como uma ferramenta de interface do usuário. No Windows, inicie um prompt de comando, execute R e, dentro do R, execute `rattle()`. No Linux, conecte-se com o X2Go, inicie um terminal, execute R e, em R, execute `rattle()`. |
| Link para amostras      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas      |LightGBM, weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca de sistema de aprendizado rápida, de código aberto e fora do núcleo    |
| Edições com suporte     | Windows, Linux     |
| Usos típicos      | Biblioteca geral de aprendizado de máquina      |
| Como ele é configurado ou instalado?      |  Windows: instalador MSI<br/>Linux: apt-get |
| Como usá-lo ou executá-lo      | Como uma ferramenta de linha de comando no caminho (`C:\Program Files\VowpalWabbit\vw.exe` no Windows, `/usr/bin/vw` no Linux)    |
| Link para amostras      | [Exemplos de VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Uma coleção de algoritmos de aprendizado de máquina para tarefas de mineração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados de seu próprio código Java. Weka contém ferramentas para pré-processamento de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições com suporte     | Windows, Linux     |
| Usos típicos      | Ferramenta de aprendizado de máquina geral     |
| Como usá-lo ou executá-lo      | No Windows, procure weka no menu **Iniciar** . No Linux, entre com X2Go e, em seguida, vá para **aplicativos** > **desenvolvimento** > **weka**. |
| Link para amostras      | [Exemplos de weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca de aumento de gradiente rápida, portátil e distribuída (GBDT, GBRT ou GBM) para Python, R, Java, escalabilidade C++, e muito mais. Ele é executado em um único computador e no Apache Hadoop e no Spark.    |
| Edições com suporte     | Windows, Linux     |
| Usos típicos      | Biblioteca geral de aprendizado de máquina      |
| Como ele é configurado ou instalado?      |  Instalado com suporte de GPU   |
| Como usá-lo ou executá-lo      | Como uma biblioteca do Python (2,7 e 3,5), pacote R e ferramenta de linha de comando no caminho (`C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows e `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Links para exemplos      | Os exemplos são incluídos na VM, em `/dsvm/tools/xgboost/demo` no Linux e `C:\dsvm\tools\xgboost\demo` no Windows.   |
| Ferramentas relacionadas      | LightGBM, MXNet   |


