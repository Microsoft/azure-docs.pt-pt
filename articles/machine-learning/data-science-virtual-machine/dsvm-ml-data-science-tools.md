---
title: Ferramentas de ciência de dados e aprendizagem de máquina
description: Saiba mais sobre as ferramentas e as estruturas de aprendizado de máquina que são pré-instalados no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: dec1589898cf6cc239dbe737519e078f1c4bc7b1
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744931"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Ferramentas de ciência de dados e aprendizado de máquina em máquinas virtuais de ciência de dados do Azure
As DSVMs (máquinas virtuais de ciência de dados) do Azure têm um conjunto avançado de ferramentas e bibliotecas para o aprendizado de máquina disponível em linguagens populares, como Python, R e Julia.

Aqui estão algumas das ferramentas e bibliotecas de aprendizado de máquina no DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>SDK do Azure Machine Learning para Python
Consulte a referência completa para o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| O que é?   |   Azure Machine Learning é um serviço de nuvem que você pode usar para desenvolver e implantar modelos de aprendizado de máquina. Pode acompanhar os seus modelos como criar, formar, dimensionar e geri-los utilizando o SDK de Python. Implementar modelos como contentores e execute-os na cloud, no local, ou no Azure IoT Edge.   |
| Edições suportadas     | Windows (ambiente Conda: AzureML), Linux (ambiente Conda: py36)    |
| Utilizações típicas      | Plataforma de aprendizado de máquina geral      |
| Como é ele configurado ou instalado?      |  Instalado com o suporte GPU   |
| A utilização ou executá-lo      | Como um SDK do Python e no CLI do Azure. Ativar o ambiente de conda `AzureML` edição do Windows *ou* para `py36` na edição de Linux.      |
| Ligar a exemplos      | Blocos de notas do Jupyter exemplo estão incluídos no `AzureML` diretório em blocos de notas.  |
| Ferramentas relacionadas      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca de aumento de gradiente rápida, portátil e distribuída (GBDT, GBRT ou GBM) para Python, R, Java, escalabilidade C++, e muito mais. Ele é executado em um único computador e no Apache Hadoop e no Spark.    |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Biblioteca geral de aprendizado de máquina      |
| Como é ele configurado ou instalado?      |  Instalado com o suporte GPU   |
| A utilização ou executá-lo      | Como uma biblioteca do Python (2,7 e 3,5), pacote R e ferramenta de linha de comando no caminho (`C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows e `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Links para amostras      | Exemplos estão incluídos na VM, na `/dsvm/tools/xgboost/demo` em Linux, e `C:\dsvm\tools\xgboost\demo` no Windows.   |
| Ferramentas relacionadas      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca de sistema de aprendizado rápida, de código aberto e fora do núcleo    |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Biblioteca geral de aprendizado de máquina      |
| Como é ele configurado ou instalado?      |  Windows: instalador MSI<br/>Linux: apt-get |
| A utilização ou executá-lo      | Como uma ferramenta de linha de comandos no caminho (`C:\Program Files\VowpalWabbit\vw.exe` no Windows, `/usr/bin/vw` no Linux)    |
| Ligar a exemplos      | [Exemplos de VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas      |XGBoost LightGBM, MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Uma coleção de algoritmos de aprendizado de máquina para tarefas de mineração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados a partir de seu próprio código de Java. Weka contém ferramentas para o processamento prévio de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de aprendizado de máquina geral     |
| A utilização ou executá-lo      | No Windows, procure weka no menu **Iniciar** . No Linux, inicie sessão com X2Go e, em seguida, aceda a **aplicativos** > **desenvolvimento** > **Weka**. |
| Ligar a exemplos      | [Exemplos de weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas      |XGBoost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma interface gráfica do usuário para Data Mining usando o R.   |
| Edições suportadas     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de mineração de dados de interface do usuário geral para R    |
| A utilização ou executá-lo      | Como uma ferramenta de interface do usuário. No Windows, inicie um prompt de comando, execute R e, em seguida, dentro `rattle()`do r, execute. No Linux, conecte-se com o X2Go, inicie um terminal, execute R e, em R `rattle()`, execute. |
| Ligar a exemplos      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas      |XGBoost LightGBM, Weka,   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| O que é?   | Uma estrutura de reforço de gradiente rápida, distribuída e de alto desempenho (GBDT, GBRT, GBM ou MART) com base em algoritmos de árvore de decisão. Ele é usado para classificação, classificação e muitas outras tarefas de aprendizado de máquina.    |
| Versões suportadas      | Windows, Linux    |
| Utilizações típicas      | Estrutura de aumento de gradiente de uso geral      |
| Como é ele configurado ou instalado?      | No Windows, LightGBM é instalado como um pacote do Python. No Linux, o executável da linha de comandos está no `/opt/LightGBM/lightgbm`, o pacote de R está instalado e pacotes de Python são instalados.     |
| Ligar a exemplos      | [Guia do LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| O que é?   | Uma plataforma de ia de software livre que dá suporte ao aprendizado de máquina na memória, distribuída, rápida e escalonável.  |
| Versões suportadas      | Linux   |
| Utilizações típicas      | Aprendizado de máquina distribuído e escalonável de uso geral   |
| Como é ele configurado ou instalado?      | H2O é instalado na `/dsvm/tools/h2o`.      |
| A utilização ou executá-lo      | Ligar à VM através da utilização X2Go. Inicie um novo terminal e execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Em seguida, iniciar um navegador da web e ligue-se ao `http://localhost:54321`.      |
| Ligar a exemplos      | Os exemplos estão disponíveis na VM no Jupyter, sob o `h2o` diretório.      |
| Ferramentas relacionadas      | O Apache Spark, MXNet, XGBoost, Sparkling Water, água profunda    |

Há várias outras bibliotecas de aprendizado de máquina em DSVMs, como o pacote popular `scikit-learn` que faz parte da distribuição Anaconda Python para DSVMs. Para verificar a lista de pacotes disponíveis no Python, R e Julia, execute os gestores de pacote respectivos.
