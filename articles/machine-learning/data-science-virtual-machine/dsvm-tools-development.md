---
title: Ferramentas de programação
titleSuffix: Azure Data Science Virtual Machine
description: Saiba mais sobre as ferramentas e os ambientes de desenvolvimento integrados disponíveis no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bc1f40760c1602d81da042bf6909e44a540d35de
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612160"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de desenvolvimento no Máquina Virtual de Ciência de Dados do Azure

O Máquina Virtual de Ciência de Dados (DSVM) agrupa várias ferramentas populares em um ambiente de desenvolvimento integrado (IDE) altamente produtivo. Aqui estão algumas ferramentas fornecidas no DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de uso geral      |
| Versões do DSVM com suporte      | Windows: Visual Studio 2017, Windows 2019 (versão prévia): Visual Studio 2019      |
| Utilizações típicas      | Desenvolvimento de software    |
| Como ele é configurado e instalado no DSVM?      | Carga de trabalho de ciência de dados (ferramentas do Python e R), do Azure carga de trabalho (Hadoop, o Data Lake), node. js, ferramentas do SQL Server, [do Azure Machine Learning para Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Graficamente, abra o Visual Studio usando o ícone da área de trabalho ou o menu **Iniciar** . Pesquise por programas (chave do logotipo do Windows + S), seguido pelo **Visual Studio**. A partir daí, você pode criar projetos em linguagens como C#, Python, R e node. js.   |
| Ferramentas relacionadas no DSVM      |     Juno de código, o r Studio, Visual Studio  |

> [!NOTE]
> Poderá receber uma mensagem que o período de avaliação expirou. Introduza as credenciais da conta Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de uso geral      |
| Versões do DSVM com suporte      | Windows, Linux     |
| Utilizações típicas      | Editor de código e integração de Git   |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) no Windows, atalho da área de trabalho ou terminal (`code`) no Linux    |
| Ferramentas relacionadas no DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>Área de trabalho RStudio

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de cliente para a linguagem R   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files\RStudio\bin\rstudio.exe`) no Windows, atalho da área de trabalho (`/usr/bin/rstudio`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>Servidor RStudio

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de cliente para a linguagem R   |
| O que é?   | IDE baseado na Web para R    |
| Versões do DSVM com suporte      | Linux      |
| Utilizações típicas      |  Desenvolvimento de R     |
| Como usá-lo e executá-lo      | Habilite o serviço com _systemctl habilite o RStudio-Server_e inicie o serviço com _systemctl Start RStudio-Server_. Em seguida, entre no RStudio Server em http:\//Your-VM-IP: 8787.       |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para a linguagem Julia   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Utilizações típicas      |  Desenvolvimento de Julia     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\JuliaPro-0.5.1.1\Juno.bat`) no Windows, atalho da área de trabalho (`/opt/JuliaPro-VERSION/Juno`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para linguagem Python    |
| Versões do DSVM com suporte      | Windows 2019 (versão prévia), Linux      |
| Utilizações típicas      |  Desenvolvimento de Python     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files\tk`) no Windows. Atalho da área de trabalho (`/usr/bin/pycharm`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
