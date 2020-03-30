---
title: Máquina sinuosa e ferramentas de ciência de dados
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as ferramentas e estruturas de aprendizagem automática que estão pré-instaladas na Máquina Virtual da Ciência dos Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282312"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Machine learning e ferramentas de ciência de dados em Máquinas Virtuais de Ciência de Dados Azure
As Máquinas Virtuais da Ciência dos Dados Azure (DSVMs) têm um rico conjunto de ferramentas e bibliotecas para machine learning disponíveis em línguas populares, como Python, R e Julia.

Aqui estão algumas das ferramentas e bibliotecas de aprendizagem automática em DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>SDK do Azure Machine Learning para Python

Consulte a referência completa para o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| O que é?   |   O Azure Machine Learning é um serviço na nuvem que pode utilizar para desenvolver e implementar modelos de aprendizagem automática. Pode rastrear os seus modelos à medida que constrói, treina, escala e gere-os utilizando o Python SDK. Desloque os modelos como recipientes e execute-os na nuvem, no local ou no Azure IoT Edge.   |
| Edições apoiadas     | Janelas (ambiente de condomínio: AzureML), Linux (ambiente conda: py36)    |
| Usos típicos      | Plataforma geral de aprendizagem automática      |
| Como é configurado ou instalado?      |  Instalado com suporte gpu   |
| Como usá-lo ou executá-lo      | Como um Python SDK e no Azure CLI. Ative para o `AzureML` ambiente de condomínio `py36` na edição do Windows *ou* na edição do Linux.      |
| Ligação a amostras      | Os cadernos Jupyter da `AzureML` amostra estão incluídos no diretório em cadernos.  |
| Ferramentas relacionadas      | Visual Studio Code   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| O que é?   | Uma plataforma de IA de código aberto que suporta a aprendizagem automática em memória, distribuída, rápida e escalável.  |
| Versões suportadas      | Linux   |
| Usos típicos      | Aprendizagem automática distribuída e escalável de uso geral   |
| Como é configurado ou instalado?      | H2O está instalado `/dsvm/tools/h2o`em .      |
| Como usá-lo ou executá-lo      | Ligue-se ao VM utilizando o X2Go. Inicie um novo terminal, e corra. `java -jar /dsvm/tools/h2o/current/h2o.jar` Em seguida, inicie um `http://localhost:54321`navegador web e ligue-se a .      |
| Ligação a amostras      | As amostras estão disponíveis no VM em Jupyter sob o `h2o` diretório.      |
| Ferramentas relacionadas      | Apache Spark, MXNet, XGBoost, Água Com Gás, Águas Profundas    |

Existem várias outras bibliotecas de aprendizagem automática `scikit-learn` em DSVMs, como o pacote popular que faz parte da distribuição da Anaconda Python para DSVMs. Para consultar a lista de pacotes disponíveis em Python, R e Julia, executar os respetivos gestores de pacotes.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| O que é?   | Uma estrutura rápida, distribuída e de alto desempenho (GBDT, GBRT, GBM ou MART) com base em algoritmos de árvores de decisão. É usado para classificação, classificação e muitas outras tarefas de aprendizagem automática.    |
| Versões suportadas      | Windows, Linux    |
| Usos típicos      | Quadro de reforço de gradientes de fim geral      |
| Como é configurado ou instalado?      | No Windows, o LightGBM está instalado como um pacote Python. No Linux, a linha de `/opt/LightGBM/lightgbm`comando executada está dentro , o pacote R está instalado e os pacotes Python são instalados.     |
| Ligação a amostras      | [Guia LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma interface gráfica do utilizador para a mineração de dados utilizando R.   |
| Edições apoiadas     | Windows, Linux     |
| Usos típicos      | Ferramenta geral de mineração de dados da UI para R    |
| Como usá-lo ou executá-lo      | Como uma ferramenta de UI. No Windows, inicie um pedido de comando, execute R, e depois dentro de R, corra `rattle()`. No Linux, ligue-se ao X2Go, inicie um terminal, `rattle()`corra R, e depois dentro de R, corra . |
| Ligação a amostras      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca rápida, de código aberto e fora do núcleo    |
| Edições apoiadas     | Windows, Linux     |
| Usos típicos      | Biblioteca geral de aprendizagem automática      |
| Como é configurado ou instalado?      |  Windows: instalador msi<br/>Linux: apt-get |
| Como usá-lo ou executá-lo      | Como uma ferramenta de comando`C:\Program Files\VowpalWabbit\vw.exe` no caminho `/usr/bin/vw` (no Windows, no Linux)    |
| Ligação a amostras      | [Amostras de Wabbit VowPal](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Uma coleção de algoritmos de aprendizagem automática para tarefas de mineração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados a partir do seu próprio código Java. Weka contém ferramentas para pré-processamento de dados, classificação, regressão, agrupamento, regras de associação e visualização. |
| Edições apoiadas     | Windows, Linux     |
| Usos típicos      | Ferramenta geral de aprendizagem automática     |
| Como usá-lo ou executá-lo      | No Windows, procure Weka no menu **Iniciar.** No Linux, inscreva-se na X2Go e, em seguida, vá para **applications** > **Development** > **Weka**. |
| Ligação a amostras      | [Amostras de Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca rápida, portátil e distribuída de reforço de gradientes (GBDT, GBRT ou GBM) para Python, R, Java, Scala, C++, e muito mais. Funciona numa única máquina, e em Apache Hadoop e Spark.    |
| Edições apoiadas     | Windows, Linux     |
| Usos típicos      | Biblioteca geral de aprendizagem automática      |
| Como é configurado ou instalado?      |  Instalado com suporte gpu   |
| Como usá-lo ou executá-lo      | Como biblioteca Python (2.7 e 3.5), pacote R e ferramenta`C:\dsvm\tools\xgboost\bin\xgboost.exe` de `/dsvm/tools/xgboost/xgboost` comando no caminho (para Windows e para Linux)    |
| Ligações a amostras      | As amostras estão incluídas `/dsvm/tools/xgboost/demo` no VM, no Linux e `C:\dsvm\tools\xgboost\demo` no Windows.   |
| Ferramentas relacionadas      | LightGBM   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| O que é?   | Motor de consulta SQL de código aberto em big data    |
| Versões DSVM suportadas      | Windows 2019, Linux  |
| Como é configurado e instalado no DSVM?      |  Instalado apenas em `/dsvm/tools/drill*` modo incorporado   |
| Usos típicos      |  Para a exploração de dados no local sem necessidade de extrato, transforme, carregue (ETL). Consulta diferentes fontes e formatos de dados, incluindo CSV, JSON, tabelas relacionais e Hadoop.     |
| Como usá-lo e executá-lo      | Atalho de secretária  <br/> [Começa com o Drill em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas no DSVM      |   Rattle, Weka, SQL Server Management Studio      |


