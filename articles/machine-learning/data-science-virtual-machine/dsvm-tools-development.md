---
title: Ferramentas de desenvolvimento
titleSuffix: Azure Data Science Virtual Machine
description: Saiba mais sobre as ferramentas e os ambientes de desenvolvimento integrados disponíveis no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 62eb5f72d4b4395602b2665c0d1b3da4f6bb459b
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950204"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de desenvolvimento no Máquina Virtual de Ciência de Dados do Azure

O Máquina Virtual de Ciência de Dados (DSVM) agrupa várias ferramentas populares em um ambiente de desenvolvimento integrado (IDE) altamente produtivo. Aqui estão algumas ferramentas fornecidas no DSVM.

## <a name="visual-studio-2017"></a>Visual Studio 2017

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de uso geral      |
| Versões do DSVM com suporte      | Windows      |
| Utilizações típicas      | Desenvolvimento de software    |
| Como ele é configurado e instalado no DSVM?      | Carga de trabalho de ciência de dados (ferramentas do Python e R), do Azure carga de trabalho (Hadoop, o Data Lake), node. js, ferramentas do SQL Server, [do Azure Machine Learning para Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Ferramentas relacionadas no DSVM      |     Juno de código, o r Studio, Visual Studio  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de uso geral      |
| Versões do DSVM com suporte      | Windows, Linux     |
| Utilizações típicas      | Editor de código e integração de Git   |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) no Windows, atalho da área de trabalho ou terminal (`code`) no Linux    |
| Ferramentas relacionadas no DSVM      |     Visual Studio 2017, o RStudio, Juno  |

## <a name="rstudio--desktop"></a>Ambiente de trabalho do RStudio 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de cliente para a linguagem R   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files\RStudio\bin\rstudio.exe`) no Windows, atalho da área de trabalho (`/usr/bin/rstudio`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>R Studio Server 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de cliente para a linguagem R   |
| O que é?   | IDE baseado na Web para R    |
| Versões do DSVM com suporte      | Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como usá-lo e executá-lo      | Habilite o serviço com _systemctl habilite o RStudio-Server_e inicie o serviço com _systemctl Start RStudio-Server_. Em seguida, entre no RStudio Server em http: \//Your-VM-IP: 8787.       |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, Visual Studio Code, ambiente de trabalho do RStudio      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para a linguagem Julia   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de Julia     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\JuliaPro-0.5.1.1\Juno.bat`) no Windows, atalho da área de trabalho (`/opt/JuliaPro-VERSION/Juno`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, o código do Visual Studio, o RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem Python    |
| Versões do DSVM com suporte      | Linux      |
| Utilizações típicas      |  Desenvolvimento de Python     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`/usr/bin/pycharm`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, o código do Visual Studio, o RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta de BI e visualização de dados interativa    |
| Versões do DSVM com suporte      | Windows  |
| Utilizações típicas      |  Visualização de dados e criação de painéis   |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

