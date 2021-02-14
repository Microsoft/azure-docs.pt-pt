---
title: Ferramentas de programação
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as ferramentas e ambientes de desenvolvimento integrados disponíveis na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cecc195b8b97ffd9b25cf12898726352ddd698a9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519444"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de desenvolvimento na Máquina Virtual Azure Data Science

A Máquina Virtual de Ciência de Dados (DSVM) agrega várias ferramentas populares num ambiente de desenvolvimento altamente produtivo integrado (IDE). Aqui estão algumas ferramentas que são fornecidas no DSVM.

## <a name="visual-studio-community-edition"></a>Edição Comunitária do Estúdio Visual

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | IDE de finalidade geral      |
| Versões DSVM suportadas      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Usos típicos      | Desenvolvimento de software    |
| Como é configurado e instalado no DSVM?      | Data Science Workload (ferramentas Python e R), carga de trabalho Azure (Hadoop, Data Lake), Node.js, ferramentas de servidor SQL, [Aprendizagem automática Azure para Código do Estúdio Visual](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usá-lo e executá-lo      | Atalho para desktop `C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe` (). Graficamente, abra o Visual Studio utilizando o ícone do ambiente de trabalho ou o menu **Iniciar.** Procure por programas (tecla de logótipo do Windows+S), seguido do **Visual Studio**. A partir daí, você pode criar projetos em línguas como C#, Python, R e Node.js.   |
| Ferramentas relacionadas no DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Pode receber uma mensagem de que o seu período de avaliação expirou. Insira as credenciais da sua conta Microsoft. Ou criar uma nova conta gratuita para ter acesso à Comunidade visual de estúdio.

## <a name="visual-studio-code"></a>Visual Studio Code 

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | IDE de finalidade geral      |
| Versões DSVM suportadas      | Windows, Linux     |
| Usos típicos      | Editor de código e integração de Git   |
| Como usá-lo e executá-lo      | Atalho de ambiente de trabalho ( `C:\Program Files (x86)\Microsoft VS Code\Code.exe` ) em Windows, atalho ou terminal de ambiente de trabalho `code` () em Linux    |
| Ferramentas relacionadas no DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | IDE do cliente para idioma R   |
| Versões DSVM suportadas      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento r     |
| Como usá-lo e executá-lo      | Atalho de ambiente de trabalho `C:\Program Files\RStudio\bin\rstudio.exe` ( ) no Windows, atalho de ambiente de trabalho ( `/usr/bin/rstudio` ) no Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código do Estúdio Visual, Juno      |

## <a name="rstudio-server"></a>RStudio Server

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | IDE do cliente para idioma R   |
| Que passa?   | IDE baseado na Web para R    |
| Versões DSVM suportadas      | Linux      |
| Usos típicos      |  Desenvolvimento r     |
| Como usá-lo e executá-lo      | Ativar o serviço com _sistemactl ativar o rstudio-servidor_ e, em seguida, iniciar o serviço com _o sistemactl start rstudio-servidor_. Em seguida, inscreva-se no RStudio Server em http: \/ /your-vm-ip:8787.       |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, Ambiente de Trabalho RStudio      |

## <a name="juno"></a>Rio Juno 

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | IDE do cliente para linguagem Julia   |
| Versões DSVM suportadas      | Windows, Linux      |
| Usos típicos      |  Júlia desenvolvimento     |
| Como usá-lo e executá-lo      | Atalho de ambiente de trabalho `C:\JuliaPro-0.5.1.1\Juno.bat` ( ) no Windows, atalho de ambiente de trabalho ( `/opt/JuliaPro-VERSION/Juno` ) no Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, RStudio      |

## <a name="pycharm"></a>Pycharm

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | IDE do cliente para idioma python    |
| Versões DSVM suportadas      | Windows 2019, Linux      |
| Usos típicos      |  Desenvolvimento de Python     |
| Como usá-lo e executá-lo      | Atalho de ambiente de trabalho `C:\Program Files\tk` () no Windows. Atalho de ambiente de trabalho `/usr/bin/pycharm` ( ) em Linux      |
| Ferramentas relacionadas no DSVM      |   Estúdio Visual, Código de Estúdio Visual, RStudio      |
