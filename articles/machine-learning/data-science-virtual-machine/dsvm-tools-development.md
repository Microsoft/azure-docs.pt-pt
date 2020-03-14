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
ms.openlocfilehash: bc1f40760c1602d81da042bf6909e44a540d35de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283761"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de desenvolvimento na Máquina Virtual de Ciência de Dados Azure

A Máquina Virtual da Ciência dos Dados (DSVM) agrega várias ferramentas populares num ambiente de desenvolvimento integrado altamente produtivo (IDE). Aqui estão algumas ferramentas fornecidas no DSVM.

## <a name="visual-studio-community-edition"></a>Edição Comunitária de Estúdio Visual

|    |           |
| ------------- | ------------- |
| O que é?   | Objetivo geral IDE      |
| Versões DSVM suportadas      | Windows: Visual Studio 2017, Windows 2019 (Pré-visualização) : Visual Studio 2019      |
| Utilizações típicas      | Desenvolvimento de software    |
| Como é configurado e instalado no DSVM?      | Carga de trabalho da Ciência dos Dados (ferramentas Python e R), carga de trabalho Azure (Hadoop, Data Lake), Node.js, ferramentas SQL Server, [Azure Machine Learning for Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usá-lo e executá-lo      | Atalho para desktop (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Graficamente, abra o Visual Studio utilizando o ícone do ambiente de trabalho ou o menu **Iniciar.** Procure programas (Windows logo key+S), seguido de **Visual Studio**. A partir daí, você pode C#criar projetos em línguas como Python, R e Node.js.   |
| Ferramentas relacionadas no DSVM      |     Juno de código, o r Studio, Visual Studio  |

> [!NOTE]
> Poderá receber uma mensagem que o período de avaliação expirou. Introduza as credenciais da conta Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| O que é?   | Objetivo geral IDE      |
| Versões DSVM suportadas      | Windows, Linux     |
| Utilizações típicas      | Editor de código e integração de Git   |
| Como usá-lo e executá-lo      | Atalho de secretária (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) no Windows, atalho de secretária ou terminal (`code`) em Linux    |
| Ferramentas relacionadas no DSVM      |     Estúdio Visual, RStudio, Juno  |

## <a name="rstudio-desktop"></a>Ambiente de trabalho rstudio

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem R   |
| Versões DSVM suportadas      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como usá-lo e executá-lo      | Atalho de secretária (`C:\Program Files\RStudio\bin\rstudio.exe`) no Windows, atalho de secretária (`/usr/bin/rstudio`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, Juno      |

## <a name="rstudio-server"></a>Servidor RStudio

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem R   |
| O que é?   | IDE baseado na Web para R    |
| Versões DSVM suportadas      | Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como usá-lo e executá-lo      | Ative o serviço com _o sistemactl ativar o rstudio-server,_ e, em seguida, iniciar o serviço com _o sistemactl iniciar o rstudio-server_. Em seguida, inscreva-se no RStudio Server em http:\//your-vm-ip:8787.       |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para a linguagem Julia   |
| Versões DSVM suportadas      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de Julia     |
| Como usá-lo e executá-lo      | Atalho de secretária (`C:\JuliaPro-0.5.1.1\Juno.bat`) no Windows, atalho de secretária (`/opt/JuliaPro-VERSION/Juno`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem Python    |
| Versões DSVM suportadas      | Windows 2019 (Pré-visualização), Linux      |
| Utilizações típicas      |  Desenvolvimento de Python     |
| Como usá-lo e executá-lo      | Atalho de secretária (`C:\Program Files\tk`) no Windows. Atalho de secretária (`/usr/bin/pycharm`) em Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, RStudio      |
