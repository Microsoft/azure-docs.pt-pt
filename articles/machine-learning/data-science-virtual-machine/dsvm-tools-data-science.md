---
title: Ferramentas de ciência de dados e aprendizagem de máquina
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
ms.openlocfilehash: c6b7e3da6cb6fd87e3b43d6f310e3b76f8fc4d30
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390604"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Machine learning e ferramentas de ciência de dados em Máquinas Virtuais de Ciência de Dados Azure
As Máquinas Virtuais da Ciência dos Dados Azure (DSVMs) têm um rico conjunto de ferramentas e bibliotecas para machine learning disponíveis em línguas populares, como Python, R e Julia.

Aqui estão algumas das ferramentas e bibliotecas de aprendizagem automática em DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>SDK do Azure Machine Learning para Python

Consulte a referência completa para o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| O que é?   |   O Azure Machine Learning é um serviço na nuvem que pode utilizar para desenvolver e implementar modelos de aprendizagem automática. Pode acompanhar os seus modelos como criar, formar, dimensionar e geri-los utilizando o SDK de Python. Implementar modelos como contentores e execute-os na cloud, no local, ou no Azure IoT Edge.   |
| Edições suportadas     | Windows (ambiente de conda: AzureML), Linux (ambiente de conda: py36)    |
| Utilizações típicas      | Plataforma geral de aprendizagem automática      |
| Como é ele configurado ou instalado?      |  Instalado com o suporte GPU   |
| A utilização ou executá-lo      | Como um Python SDK e no Azure CLI. Ative para o ambiente de condomínio `AzureML` na edição do Windows *ou* para `py36` na edição do Linux.      |
| Ligar a exemplos      | Os cadernos Jupyter da amostra estão incluídos no diretório `AzureML` em cadernos.  |
| Ferramentas relacionadas      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| O que é?   | Uma plataforma de IA de código aberto que suporta a aprendizagem automática em memória, distribuída, rápida e escalável.  |
| Versões suportadas      | Linux   |
| Utilizações típicas      | Aprendizagem automática distribuída e escalável de uso geral   |
| Como é ele configurado ou instalado?      | O H2O está instalado em `/dsvm/tools/h2o`.      |
| A utilização ou executá-lo      | Ligar à VM através da utilização X2Go. Inicie um novo terminal e `java -jar /dsvm/tools/h2o/current/h2o.jar`. Em seguida, inicie um navegador web e ligue-se a `http://localhost:54321`.      |
| Ligar a exemplos      | As amostras estão disponíveis no VM em Jupyter sob o diretório `h2o`.      |
| Ferramentas relacionadas      | O Apache Spark, MXNet, XGBoost, Sparkling Water, água profunda    |

Existem várias outras bibliotecas de aprendizagem automática em DSVMs, como o popular pacote de `scikit-learn` que faz parte da distribuição da Anaconda Python para DSVMs. Para verificar a lista de pacotes disponíveis no Python, R e Julia, execute os gestores de pacote respectivos.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| O que é?   | Uma estrutura rápida, distribuída e de alto desempenho (GBDT, GBRT, GBM ou MART) com base em algoritmos de árvores de decisão. É usado para classificação, classificação e muitas outras tarefas de aprendizagem automática.    |
| Versões suportadas      | Windows, Linux    |
| Utilizações típicas      | Quadro de reforço de gradientes de fim geral      |
| Como é ele configurado ou instalado?      | No Windows, LightGBM é instalado como um pacote do Python. No Linux, a linha de comando executada está em `/opt/LightGBM/lightgbm`, o pacote R está instalado e os pacotes Python são instalados.     |
| Ligar a exemplos      | [Guia LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma interface gráfica do utilizador para a mineração de dados utilizando R.   |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Ferramenta geral de mineração de dados da UI para R    |
| A utilização ou executá-lo      | Como uma ferramenta de UI. No Windows, inicie um pedido de comando, execute R, e depois dentro de R, executar `rattle()`. No Linux, ligue-se ao X2Go, inicie um terminal, corra R, e depois dentro de R, corra `rattle()`. |
| Ligar a exemplos      | [Chocalho](https://togaware.com/onepager/) |
| Ferramentas relacionadas      |XGBoost LightGBM, Weka,   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca rápida, de código aberto e fora do núcleo    |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Biblioteca geral de aprendizagem automática      |
| Como é ele configurado ou instalado?      |  Windows: instalador msi<br/>Linux: apt-get |
| A utilização ou executá-lo      | Como uma ferramenta de comando no caminho (`C:\Program Files\VowpalWabbit\vw.exe` no Windows, `/usr/bin/vw` no Linux)    |
| Ligar a exemplos      | [Amostras de Wabbit VowPal](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas      |XGBoost LightGBM, MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Uma coleção de algoritmos de aprendizagem automática para tarefas de mineração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados a partir de seu próprio código de Java. Weka contém ferramentas para o processamento prévio de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Ferramenta geral de aprendizagem automática     |
| A utilização ou executá-lo      | No Windows, procure Weka no menu **Iniciar.** No Linux, inscreva-se na X2Go e, em seguida, vá a **Aplicações** > **Desenvolvimento** > **Weka.** |
| Ligar a exemplos      | [Amostras de Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas      |XGBoost LightGBM, Rattle,   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca rápida, portátil e distribuída de reforço de gradientes (GBDT, GBRT ou GBM) C++para Python, R, Java, Scala, e muito mais. Funciona numa única máquina, e em Apache Hadoop e Spark.    |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Biblioteca geral de aprendizagem automática      |
| Como é ele configurado ou instalado?      |  Instalado com o suporte GPU   |
| A utilização ou executá-lo      | Como biblioteca Python (2.7 e 3.5), pacote R e ferramenta de comando no caminho (`C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows e `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Links para amostras      | As amostras estão incluídas no VM, em `/dsvm/tools/xgboost/demo` no Linux, e `C:\dsvm\tools\xgboost\demo` no Windows.   |
| Ferramentas relacionadas      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| O que é?   | Motor de consulta SQL de código aberto em big data    |
| Versões DSVM suportadas      | Windows 2019 (Pré-visualização), Linux  |
| Como é configurado e instalado no DSVM?      |  Instalado em `/dsvm/tools/drill*` apenas em modo incorporado   |
| Utilizações típicas      |  Para a exploração de dados no local sem necessidade de extrato, transforme, carregue (ETL). Consulta diferentes fontes e formatos de dados, incluindo CSV, JSON, tabelas relacionais e Hadoop.     |
| Como usá-lo e executá-lo      | Atalho de secretária  <br/> [Começa com o Drill em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas no DSVM      |   Rattle, Weka, SQL Server Management Studio      |


