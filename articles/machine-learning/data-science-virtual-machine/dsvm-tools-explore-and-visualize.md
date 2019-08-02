---
title: Ferramentas de exploração e visualização de dados – Azure | Microsoft Docs
description: Ferramentas de exploração e visualização de dados para o Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 31b05ec4fa68c3d4804000caee94b62432bdaed9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557760"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Ferramentas de exploração e visualização de dados no Máquina Virtual de Ciência de Dados

Uma etapa importante na ciência de dados é entender os dados. As ferramentas de visualização e exploração de dados ajudam a acelerar a compreensão dos dados. Aqui estão algumas ferramentas fornecidas no DSVM que facilitam essa etapa. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| O que é?   | Mecanismo de consulta SQL de código-fonte aberto em Big data    |
| Versões suportadas DSVM      | Windows, Linux  |
| Como é ele configurado / instalado no DSVM?      |  Instalado somente no no modo inserido `/dsvm/tools/drill*`   |
| Utilizações típicas      |  Exploração de dados in-situ sem exigir ETL. Consultar diferentes fontes de dados e formatos, incluindo CSV, JSON, tabelas relacionais, Hadoop     |
| Como utilizar / executá-lo?      | Atalho da área de trabalho  <br/> [Introdução à análise em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas na DSVM      |   Rattle, weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Weka é uma coleção de algoritmos de aprendizado de máquina para tarefas de Data Mining. Os algoritmos podem ser aplicados diretamente a um DataSet ou chamados de seu próprio código Java. Weka contém ferramentas para o processamento prévio de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições DSVM suportados     | Windows, Linux     |
| Utilizações típicas      | Ferramenta ML geral     |
| Como utilizar / executá-lo?      | No Windows, procure weka no menu iniciar. No Linux, entre com X2Go e navegue até Applications-> Development-> weka. |
| Links para amostras      | [Exemplos de weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas na DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma interface gráfica do usuário para mineração de dados usando o R   |
| Edições DSVM suportados     | Windows, Linux     |
| Utilizações típicas      | Ferramenta de mineração de dados de interface do usuário geral para R    |
| Como utilizar / executá-lo?      | Ferramenta de interface do Usuário. No Windows, inicie um prompt de comando, execute R e, em seguida `rattle()`, dentro do r, execute. No Linux, conecte-se com o X2Go, inicie um terminal, execute R e, `rattle()`em seguida, dentro do r, execute. |
| Links para amostras      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas na DSVM      |LightGBM, weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| O que é?   | Visualização interativa de dados e ferramenta de BI    |
| Versões suportadas DSVM      | Windows  |
| Utilizações típicas      |  Visualização de dados e a criação de Dashboards   |
| Como utilizar / executá-lo?      | Atalho de Desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Ferramentas relacionadas na DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

