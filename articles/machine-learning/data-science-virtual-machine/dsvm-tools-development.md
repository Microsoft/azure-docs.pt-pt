---
title: Ferramentas de desenvolvimento de Máquina Virtual de ciência de dados - Azure | Documentos da Microsoft
description: Saiba mais sobre as ferramentas e os ambientes de desenvolvimento integrados pré-instalados no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: vijetajo
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
ms.author: vijetaj
ms.openlocfilehash: 8f9dad0fb007945b69b75daadfdb12f61dc4defb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074293"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Ferramentas de desenvolvimento na máquina de Virtual de ciência de dados

O Máquina Virtual de Ciência de Dados (DSVM) agrupa várias ferramentas populares em um ambiente de desenvolvimento integrado (IDE) altamente produtivo. Aqui estão algumas ferramentas fornecidas no DSVM.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de uso geral      |
| Versões do DSVM com suporte      | Windows      |
| Utilizações típicas      | Desenvolvimento de software    |
| Como ele é configurado e instalado no DSVM?      | Carga de trabalho de ciência de dados (ferramentas do Python e R), do Azure carga de trabalho (Hadoop, o Data Lake), node. js, ferramentas do SQL Server, [do Azure Machine Learning para Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usá-lo e executá-lo      | Atalho da área`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`de trabalho ()    |
| Ferramentas relacionadas no DSVM      |     Juno de código, o r Studio, Visual Studio  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de uso geral      |
| Versões do DSVM com suporte      | Windows, Linux     |
| Utilizações típicas      | Editor de código e integração de Git   |
| Como usá-lo e executá-lo      | Atalho da área`C:\Program Files (x86)\Microsoft VS Code\Code.exe`de trabalho () no Windows, atalho da`code`área de trabalho ou terminal () no Linux    |
| Ferramentas relacionadas no DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>Ambiente de trabalho do RStudio 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de cliente para a linguagem R   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como usá-lo e executá-lo      | Atalho da área`C:\Program Files\RStudio\bin\rstudio.exe`de trabalho () no Windows,`/usr/bin/rstudio`atalho da área de trabalho () no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>R Studio Server 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de cliente para a linguagem R   |
| O que é?   | IDE baseado na Web para R    |
| Versões do DSVM com suporte      | Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como usá-lo e executá-lo      | Habilite o serviço com _systemctl habilite o RStudio-Server_e inicie o serviço com _systemctl Start RStudio-Server_. Em seguida, entre no RStudio Server em http\/:/Your-VM-IP: 8787.       |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2019, Visual Studio Code, área de trabalho RStudio      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para a linguagem Julia   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de Julia     |
| Como usá-lo e executá-lo      | Atalho da área`C:\JuliaPro-0.5.1.1\Juno.bat`de trabalho () no Windows,`/opt/JuliaPro-VERSION/Juno`atalho da área de trabalho () no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem Python    |
| Versões do DSVM com suporte      | Linux      |
| Utilizações típicas      |  Desenvolvimento de Python     |
| Como usá-lo e executá-lo      | Atalho da área`/usr/bin/pycharm`de trabalho () no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta de BI e visualização de dados interativa    |
| Versões do DSVM com suporte      | Windows  |
| Utilizações típicas      |  Visualização de dados e criação de painéis   |
| Como usá-lo e executá-lo      | Atalho da área`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`de trabalho ()      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

