---
title: Ferramentas de desenvolvimento de Máquina Virtual de ciência de dados - Azure | Documentos da Microsoft
description: Saiba mais sobre as ferramentas e ambientes de desenvolvimento integrado que vêm pré-instaladas na máquina de Virtual de ciência de dados.
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
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: dd60c5d0210ffba373839fd0f194496c5dbcc20d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999399"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Ferramentas de desenvolvimento na máquina de Virtual de ciência de dados

A Máquina Virtual de ciência de dados (DSVM) fornece um ambiente produtivo para o desenvolvimento, várias ferramentas populares e IDE de agrupamento. Aqui estão algumas ferramentas fornecidas no DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  

|    |           |
| ------------- | ------------- |
| O que é?   | IDE para fins gerais      |
| Versões suportadas DSVM      | Windows      |
| Utilizações típicas      | Desenvolvimento de software    |
| Como é ele configurado / instalado no DSVM?      | Carga de trabalho de ciência de dados (ferramentas do Python e R), do Azure carga de trabalho (Hadoop, o Data Lake), node. js, ferramentas do SQL Server, [do Azure Machine Learning para Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como utilizar / executá-lo?      | Atalho de Desktop (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Ferramentas relacionadas na DSVM      |     Juno de código, o r Studio, Visual Studio  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE para fins gerais      |
| Versões suportadas DSVM      | Windows, Linux     |
| Utilizações típicas      | Editor de código e integração de Git   |
| Como utilizar / executá-lo?      | Atalho de Desktop (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) no Windows, o atalho de desktop ou o terminal (`code`) no Linux    |
| Ferramentas relacionadas na DSVM      |     Visual Studio 2017, o RStudio, Juno  |

## <a name="rstudio--desktop"></a>Ambiente de trabalho do RStudio 

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE de R    |
| Versões suportadas DSVM      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como utilizar / executá-lo?      | Atalho de Desktop (`C:\Program Files\RStudio\bin\rstudio.exe`) no Windows, atalho de Desktop (`/usr/bin/rstudio`) no Linux      |
| Ferramentas relacionadas na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>R Studio Server 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE baseado na Web para R    |
| Versões suportadas DSVM      | Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como utilizar / executá-lo?      | Ativar o serviço com _systemctl ativar r Studio server_, em seguida, inicie o serviço com _systemctl iniciar rstudio server_. Em seguida, pode iniciar sessão RStudio Server na http://your-vm-ip:8787.       |
| Ferramentas relacionadas na DSVM      |   Visual Studio 2017, Visual Studio Code, ambiente de trabalho do RStudio      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para a linguagem Julia   |
| Versões suportadas DSVM      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de Julia     |
| Como utilizar / executá-lo?      | Atalho de Desktop (`C:\JuliaPro-0.5.1.1\Juno.bat`) no Windows, atalho de Desktop (`/opt/JuliaPro-VERSION/Juno`) no Linux      |
| Ferramentas relacionadas na DSVM      |   Visual Studio 2017, o código do Visual Studio, o RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem Python    |
| Versões suportadas DSVM      | Linux      |
| Utilizações típicas      |  Desenvolvimento de Python     |
| Como utilizar / executá-lo?      | Atalho de Desktop (`/usr/bin/pycharm`) no Linux      |
| Ferramentas relacionadas na DSVM      |   Visual Studio 2017, o código do Visual Studio, o RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 

|    |           |
| ------------- | ------------- |
| O que é?   | Visualização interativa de dados e ferramenta de BI    |
| Versões suportadas DSVM      | Windows  |
| Utilizações típicas      |  Visualização de dados e a criação de Dashboards   |
| Como utilizar / executá-lo?      | Atalho de Desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Ferramentas relacionadas na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

