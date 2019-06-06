---
title: Exploração e visualização ferramentas de dados - Azure | Documentos da Microsoft
description: Exploração e visualização ferramentas de dados para a máquina de Virtual de ciência de dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 693be80e493a0ba259d147f432dc9d6c07ba876d
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427525"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Ferramentas de exploração e visualização de dados na máquina de Virtual de ciência de dados

É uma etapa importante da ciência de dados compreender os dados. Visualização e ferramentas de exploração de dados ajudam a acelerar a compreensão de dados. Aqui estão algumas ferramentas fornecidas no DSVM que facilitam a nessa importante etapa. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| O que é?   | Motor de consulta SQL de código-fonte aberto de macrodados    |
| Versões suportadas DSVM      | Windows, Linux  |
| Como é ele configurado / instalado no DSVM?      |  Instalado em `/dsvm/tools/drill*` no modo de embedded apenas   |
| Utilizações típicas      |  Exploração de dados no situ sem a necessidade de ETL. Consultar a diferentes origens de dados e formatos, incluindo CSV, JSON, tabelas relacionais, Hadoop     |
| Como utilizar / executá-lo?      | Atalho de Desktop  <br/> [Introdução ao teste em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas na DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Weka é uma coleção de algoritmos de machine learning para tarefas de extração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados a partir de seu próprio código de Java. Weka contém ferramentas para o processamento prévio de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições DSVM suportados     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de ML geral     |
| Como utilizar / executá-lo?      | No Windows, procure Weka no Menu Iniciar. No Linux, inicie sessão com X2Go, em seguida, navegue para aplicativos -> desenvolvimento -> Weka. |
| Links para amostras      | [Exemplos de weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas na DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma Interface gráfica do usuário para Data Mining com R   |
| Edições DSVM suportados     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de mineração de dados de interface de Usuário geral para R    |
| Como utilizar / executá-lo?      | Ferramenta de interface do Usuário. No Windows, inicie uma linha de comandos, execute o R, em seguida, dentro de R executar `rattle()`. No Linux, conecte-se X2Go, iniciar um terminal, execute R, em seguida, dentro de R executar `rattle()`. |
| Links para amostras      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas na DSVM      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| O que é?   | Visualização interativa de dados e ferramenta de BI    |
| Versões suportadas DSVM      | Windows  |
| Utilizações típicas      |  Visualização de dados e a criação de Dashboards   |
| Como utilizar / executá-lo?      | Atalho de Desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Ferramentas relacionadas na DSVM      |   2019 do Visual Studio, Visual Studio Code, Juno      |

