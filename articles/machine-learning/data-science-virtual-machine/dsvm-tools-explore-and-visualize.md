---
title: Ferramentas de exploração e visualização de dados
titleSuffix: Azure Data Science Virtual Machine
description: Ferramentas de exploração e visualização de dados para o Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 14c34a13440d50fcf42c2207f9933dc1de014a14
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191977"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de exploração e visualização de dados no Máquina Virtual de Ciência de Dados do Azure

Na ciência de dados, a chave é entender os dados. As ferramentas de visualização e exploração de dados ajudam a acelerar a compreensão dos dados. As ferramentas a seguir, que são fornecidas no Máquina Virtual de Ciência de Dados (DSVM), facilitam a etapa dessa chave.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| O que é?   | Mecanismo de consulta SQL de código aberto no Big Data    |
| Versões do DSVM com suporte      | Windows, Linux  |
| Como ele é configurado e instalado no DSVM?      |  Instalado somente no no modo inserido `/dsvm/tools/drill*`   |
| Utilizações típicas      |  Para a exploração de dados in-loco sem a necessidade de extração, transformação, carregamento (ETL). Consulte diferentes fontes de dados e formatos, incluindo CSV, JSON, tabelas relacionais e Hadoop.     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho  <br/> [Introdução à análise em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas no DSVM      |   Rattle, weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Uma coleção de algoritmos de aprendizado de máquina para tarefas de Data Mining. Esses algoritmos podem ser aplicados diretamente a um DataSet ou chamados de seu próprio código Java. Weka contém ferramentas para pré-processamento de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições DSVM suportados     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de aprendizado de máquina geral     |
| Como usá-lo e executá-lo      | No Windows, procure Weka no menu Iniciar. No Linux, entre com X2Go e vá para aplicativos > desenvolvimento > weka. |
| Links para amostras      | [Exemplos de weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas no DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma GUI (interface gráfica do usuário) para Data Mining usando R   |
| Edições DSVM suportados     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de mineração de dados de interface do usuário geral para R    |
| Como usá-lo e executá-lo      | Ferramenta de interface do Usuário. No Windows, abra um prompt de comando, execute R e, dentro do R, `rattle()`execute. No Linux, conecte-se com o X2Go, inicie um terminal, execute R e, em R `rattle()`, execute. |
| Links para amostras      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas no DSVM      |LightGBM, weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta de BI e visualização de dados interativa    |
| Versões do DSVM com suporte      | Windows  |
| Utilizações típicas      |  Visualização de dados e criação de painéis   |
| Como usá-lo e executá-lo      | Atalho da área`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`de trabalho ()      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

