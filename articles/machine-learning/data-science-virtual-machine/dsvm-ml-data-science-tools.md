---
title: Machine learning e os dados de ferramentas de ciência - Azure | Documentos da Microsoft
description: Saiba mais sobre o machine learning, ferramentas e estruturas previamente instaladas na máquina de Virtual de ciência de dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: e8876306e4ffbd0fa9a8aafc6d5d757fd3c9c614
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847370"
---
# <a name="machine-learning-and-data-science-tools"></a>Ferramentas de ciência de dados e aprendizagem de máquina
Máquinas de virtuais de ciência de dados do Azure tem um conjunto avançado de ferramentas e bibliotecas para machine learning (ML) disponível em linguagens populares, como Python, R e Julia. 

Aqui estão algumas das ferramentas de ML e bibliotecas em máquinas de virtuais de ciência de dados. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| O que é?   |   O Azure Machine Learning é um serviço cloud que pode utilizar para desenvolver e implementar modelos de ML. Pode acompanhar os seus modelos como criar, formar, dimensionar e geri-los utilizando o SDK de Python. Implementar modelos como contentores e execute-os na cloud, no local, ou no Azure IoT Edge.   |
| Edições suportadas     | Windows (ambiente de conda: AzureML), Linux (conda environment: py36)    |
| Utilizações típicas      | Plataforma de ML geral      |
| Como é ele configurado ou instalado?      |  Instalado com o suporte GPU   |
| A utilização ou executá-lo      | Como o Python SDK e CLI do Azure. Ativar o ambiente de conda `AzureML` edição do Windows *ou* para `py36` na edição de Linux.      |
| Ligar a exemplos      | Blocos de notas do Jupyter exemplo estão incluídos no `AzureML` diretório em blocos de notas.  |
| Ferramentas relacionadas      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| O que é?   |    XGBoost é um dégradé rápido, portáteis e distribuído adaptativo biblioteca (GBDT, GBRT ou GBM) para o Python, R, Java, Scala, C++ e muito mais. Ele é executado numa única máquina, Hadoop e Spark.    |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Biblioteca de ML geral      |
| Como é ele configurado ou instalado?      |  Instalado com o suporte GPU   |
| A utilização ou executá-lo      | Como o Python library (2.7 e 3.5), o pacote de R e na ferramenta de linha de comando do caminho (`C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows, `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Links para amostras      | Exemplos estão incluídos na VM, na `/dsvm/tools/xgboost/demo` em Linux, e `C:\dsvm\tools\xgboost\demo` no Windows.   |
| Ferramentas relacionadas      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| O que é?   |   Vowpal Wabbit (também conhecido como "VW") é uma código-fonte aberto, rápidas, biblioteca de sistema de aprendizagem fora de núcleos.    |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Biblioteca de ML geral      |
| Como é ele configurado ou instalado?      |  Windows – o instalador msi, Linux – apt-get |
| A utilização ou executá-lo      | Como uma ferramenta de linha de comandos no caminho (`C:\Program Files\VowpalWabbit\vw.exe` no Windows, `/usr/bin/vw` no Linux)    |
| Ligar a exemplos      | [Exemplos de VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas      |XGBoost LightGBM, MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Weka é uma coleção de algoritmos de ML para tarefas de extração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados a partir de seu próprio código de Java. Weka contém ferramentas para o processamento prévio de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de ML geral     |
| A utilização ou executá-lo      | No Windows, procure Weka no menu Iniciar. No Linux, inicie sessão com X2Go e, em seguida, aceda a **aplicativos** > **desenvolvimento** > **Weka**. |
| Ligar a exemplos      | [Exemplos de weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas      |XGBoost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Rattle é uma interface gráfica do usuário para extração de dados com o R.   |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de extração de dados de interface de Usuário geral para R    |
| A utilização ou executá-lo      | Ferramenta de interface do Usuário. No Windows, inicie um prompt de comando, execute o R e, em seguida, dentro de R executar `rattle()`. No Linux, ligue-se a X2Go, inicie um terminal, execute R e, em seguida, dentro de R execute `rattle()`. |
| Ligar a exemplos      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas      |XGBoost LightGBM, Weka,   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| O que é?   | LightGBM é um dégradé rápido, distribuído e de alto desempenho adaptativo framework (GBDT, GBRT, GBM ou MART) com base em algoritmos de árvore de decisão. É utilizado para classificação, classificação e muitas outras tarefas de ML.    |
| Versões suportadas      | Windows, Linux    |
| Utilizações típicas      | Framework adaptativo de gradação para fins gerais      |
| Como é ele configurado ou instalado?      | No Windows, LightGBM é instalado como um pacote do Python. No Linux, o executável da linha de comandos está no `/opt/LightGBM/lightgbm`, o pacote de R está instalado e pacotes de Python são instalados.     |
| Ligar a exemplos      | [Guia de LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| O que é?   | H2O é uma plataforma de ia de código-fonte aberto que suporta ML na memória, distribuída, rápida e dimensionável.  |
| Versões suportadas      | Linux   |
| Utilizações típicas      | ML de distribuídas e dimensionável para fins gerais   |
| Como é ele configurado ou instalado?      | H2O é instalado na `/dsvm/tools/h2o`.      |
| A utilização ou executá-lo      | Ligar à VM através da utilização X2Go. Inicie um novo terminal e execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Em seguida, iniciar um navegador da web e ligue-se ao `http://localhost:54321`.      |
| Ligar a exemplos      | Os exemplos estão disponíveis na VM no Jupyter, sob o `h2o` diretório.      |
| Ferramentas relacionadas      | O Apache Spark, MXNet, XGBoost, Sparkling Water, água profunda    |

Existem várias outras bibliotecas de ML em máquinas de virtuais de ciência de dados, tais como o popular `scikit-learn` pacote que vem como parte da distribuição Anaconda Python que está instalada nas máquinas de virtuais de ciência de dados. Para verificar a lista de pacotes disponíveis no Python, R e Julia, execute os gestores de pacote respectivos.
