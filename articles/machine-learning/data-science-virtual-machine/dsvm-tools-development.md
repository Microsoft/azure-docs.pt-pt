---
title: Ferramentas de programação
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as ferramentas e ambientes integrados de desenvolvimento disponíveis na Máquina Virtual da Ciência dos Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282686"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de desenvolvimento na Máquina Virtual de Ciência de Dados Azure

A Máquina Virtual da Ciência dos Dados (DSVM) agrega várias ferramentas populares num ambiente de desenvolvimento integrado altamente produtivo (IDE). Aqui estão algumas ferramentas que são fornecidas no DSVM.

## <a name="visual-studio-community-edition"></a>Edição Comunitária de Estúdio Visual

|    |           |
| ------------- | ------------- |
| O que é?   | Objetivo geral IDE      |
| Versões DSVM suportadas      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Usos típicos      | Desenvolvimento de software    |
| Como é configurado e instalado no DSVM?      | Carga de trabalho da Ciência dos Dados (ferramentas Python e R), carga de trabalho Azure (Hadoop, Data Lake), Node.js, ferramentas SQL Server, [Azure Machine Learning for Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usá-lo e executá-lo      | Atalho para`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`desktop (). Graficamente, abra o Visual Studio utilizando o ícone do ambiente de trabalho ou o menu **Iniciar.** Procure programas (Windows logo key+S), seguido de **Visual Studio**. A partir daí, você pode criar projetos em línguas como C#, Python, R e Node.js.   |
| Ferramentas relacionadas no DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Pode receber uma mensagem de que o seu período de avaliação expirou. Insira as credenciais da sua conta Microsoft. Ou criar uma nova conta gratuita para ter acesso à Comunidade visual studio.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| O que é?   | Objetivo geral IDE      |
| Versões DSVM suportadas      | Windows, Linux     |
| Usos típicos      | Editor de código e integração git   |
| Como usá-lo e executá-lo      | Atalho de`C:\Program Files (x86)\Microsoft VS Code\Code.exe`secretária () em Windows,`code`atalho de secretária ou terminal () em Linux    |
| Ferramentas relacionadas no DSVM      |     Estúdio Visual, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem R   |
| Versões DSVM suportadas      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento R     |
| Como usá-lo e executá-lo      | Atalho de`C:\Program Files\RStudio\bin\rstudio.exe`secretária () no`/usr/bin/rstudio`Windows, atalho de secretária () no Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem R   |
| O que é?   | IDE baseado na Web para R    |
| Versões DSVM suportadas      | Linux      |
| Usos típicos      |  Desenvolvimento R     |
| Como usá-lo e executá-lo      | Ative o serviço com _o sistemactl ativar o rstudio-server,_ e, em seguida, iniciar o serviço com _o sistemactl iniciar o rstudio-server_. Em seguida, inscreva-se no\/RStudio Server em http: /your-vm-ip:8787.       |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, RStudio Desktop      |

## <a name="juno"></a>Rio Juno 

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para a língua julia   |
| Versões DSVM suportadas      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento de Júlia     |
| Como usá-lo e executá-lo      | Atalho de`C:\JuliaPro-0.5.1.1\Juno.bat`secretária () no`/opt/JuliaPro-VERSION/Juno`Windows, atalho de secretária () no Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem Python    |
| Versões DSVM suportadas      | Windows 2019, Linux      |
| Usos típicos      |  Desenvolvimento python     |
| Como usá-lo e executá-lo      | Atalho de`C:\Program Files\tk`ambiente de trabalho () no Windows. Atalho de`/usr/bin/pycharm`secretária () em Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, RStudio      |
