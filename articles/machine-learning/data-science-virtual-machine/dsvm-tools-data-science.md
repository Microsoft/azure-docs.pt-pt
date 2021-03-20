---
title: Ferramentas de aprendizagem automática e de ciência de dados
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as ferramentas e estruturas de machine-learning que estão pré-instaladas na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: ac71d8d7830f1d740d0156886a364b1d53ab247f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519563"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Ferramentas de aprendizagem automática e ciência de dados em Azure Data Science Virtual Machines
As máquinas virtuais Azure Data Science (DSVMs) têm um rico conjunto de ferramentas e bibliotecas para machine learning disponíveis em línguas populares, como Python, R e Julia.

Aqui estão algumas das ferramentas de aprendizagem automática e bibliotecas em DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>SDK do Azure Machine Learning para Python

Consulte a referência completa para o [Azure Machine Learning SDK para Python](../overview-what-is-azure-ml.md).

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   |   A azure Machine Learning é um serviço de nuvem que pode usar para desenvolver e implementar modelos de aprendizagem automática. Você pode rastrear seus modelos à medida que constrói, treina, escala e gere-os usando o Python SDK. Implemente os modelos como recipientes e olo na nuvem, no local ou no Azure IoT Edge.   |
| Edições apoiadas     | Janelas (ambiente conda: AzureML), Linux (ambiente conda: py36)    |
| Usos típicos      | Plataforma geral de aprendizagem automática      |
| Como é configurado ou instalado?      |  Instalado com suporte gpu   |
| Como usá-lo ou executá-lo      | Como um Python SDK e no Azure CLI. Ative para o ambiente conda `AzureML` na edição do Windows *ou* `py36` na edição linux.      |
| Ligação a amostras      | Os cadernos da amostra jupyter estão incluídos no `AzureML` diretório sob cadernos.  |
| Ferramentas relacionadas      | Código de Estúdio Visual, Jupyter   |

## <a name="h2o"></a>H2O

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Uma plataforma de IA de código aberto que suporta aprendizagem automática na memória, distribuída, rápida e escalável.  |
| Versões suportadas      | Linux   |
| Usos típicos      | Aprendizagem de máquinas distribuídas de forma geral e escaláveis   |
| Como é configurado ou instalado?      | H2O está instalado em `/dsvm/tools/h2o` .      |
| Como usá-lo ou executá-lo      | Ligue-se ao VM utilizando o X2Go. Iniciar um novo terminal, e `java -jar /dsvm/tools/h2o/current/h2o.jar` correr. Em seguida, inicie um navegador web e ligue-se a `http://localhost:54321` .      |
| Ligação a amostras      | As amostras estão disponíveis no VM em Jupyter sob o `h2o` diretório.      |
| Ferramentas relacionadas      | Apache Spark, MXNet, XGBoost, Água Espumante, Águas Profundas    |

Existem várias outras bibliotecas de aprendizagem automática em DSVMs, como o pacote popular `scikit-learn` que faz parte da distribuição de Anaconda Python para DSVMs. Para conferir mais informações sobre a lista de pacotes disponíveis em Python, R e Julia, execute os respetivos gestores de pacotes.

## <a name="lightgbm"></a>LightGBM

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Um quadro rápido, distribuído e de alto desempenho (GBDT, GBRT, GBM ou MART) baseado em algoritmos de árvores de decisão. É usado para classificar, classificar e muitas outras tarefas de aprendizagem automática.    |
| Versões suportadas      | Windows, Linux    |
| Usos típicos      | Quadro de reforço do gradiente de fim geral      |
| Como é configurado ou instalado?      | No Windows, o LightGBM é instalado como um pacote Python. No Linux, a linha de comando executável está `/opt/LightGBM/lightgbm` dentro, o pacote R está instalado e os pacotes Python são instalados.     |
| Ligação a amostras      | [Guia LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas      | MXNet  |

## <a name="rattle"></a>Rattle
| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   |   Uma interface gráfica do utilizador para a mineração de dados utilizando R.   |
| Edições apoiadas     | Windows, Linux     |
| Usos típicos      | Ferramenta geral de mineração de dados de UI para R    |
| Como usá-lo ou executá-lo      | Como uma ferramenta de UI. No Windows, inicie um pedido de comando, corra R e, em seguida, dentro de R, corra `rattle()` . No Linux, ligue-se ao X2Go, inicie um terminal, corra R e, em seguida, dentro de R, corra `rattle()` . |
| Ligação a amostras      | [Chocalho](https://togaware.com/onepager/) |
| Ferramentas relacionadas      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   |   Uma biblioteca rápida, de código aberto, fora do núcleo    |
| Edições apoiadas     | Windows, Linux     |
| Usos típicos      | Biblioteca geral de aprendizagem automática      |
| Como é configurado ou instalado?      |  Windows: instalador msi<br/>Linux: apt-get |
| Como usá-lo ou executá-lo      | Como uma ferramenta de linha de comando no caminho `C:\Program Files\VowpalWabbit\vw.exe` (no Windows, `/usr/bin/vw` no Linux)    |
| Ligação a amostras      | [Amostras de Wabbit VowPal](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   |  Uma recolha de algoritmos de aprendizagem automática para tarefas de mineração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados a partir do seu próprio código Java. Weka contém ferramentas para pré-processamento de dados, classificação, regressão, agrupamento, regras de associação e visualização. |
| Edições apoiadas     | Windows, Linux     |
| Usos típicos      | Ferramenta geral de aprendizagem automática     |
| Como usá-lo ou executá-lo      | No Windows, procure weka no menu **Iniciar.** No Linux, inscreva-se com x2Go e, em seguida, vá ao **Applications**  >  **Development**  >  **Weka**. |
| Ligação a amostras      | [Amostras de Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   |   Uma biblioteca rápida, portátil e distribuída de gradiente (GBDT, GBRT ou GBM) para Python, R, Java, Scala, C++, e muito mais. Funciona numa única máquina, e em Apache Hadoop e Spark.    |
| Edições apoiadas     | Windows, Linux     |
| Usos típicos      | Biblioteca geral de aprendizagem automática      |
| Como é configurado ou instalado?      |  Instalado com suporte gpu   |
| Como usá-lo ou executá-lo      | Como uma biblioteca Python (2.7 e 3.5), pacote R e ferramenta de linha de comando on-path `C:\dsvm\tools\xgboost\bin\xgboost.exe` (para Windows e `/dsvm/tools/xgboost/xgboost` linux)    |
| Ligações a amostras      | As amostras estão incluídas no VM, `/dsvm/tools/xgboost/demo` no Linux e `C:\dsvm\tools\xgboost\demo` no Windows.   |
| Ferramentas relacionadas      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Motor de consulta SQL de código aberto em big data    |
| Versões DSVM suportadas      | Windows 2019, Linux  |
| Como é configurado e instalado no DSVM?      |  Instalado apenas `/dsvm/tools/drill*` em modo incorporado   |
| Usos típicos      |  Para a exploração de dados no local sem necessidade de extrato, transformação, carga (ETL). Consultar diferentes fontes de dados e formatos, incluindo CSV, JSON, tabelas relacionais e Hadoop.     |
| Como usá-lo e executá-lo      | Atalho de ambiente de trabalho  <br/> [Começa com a Drill em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas no DSVM      |   Rattle, Weka, SQL Server Management Studio      |