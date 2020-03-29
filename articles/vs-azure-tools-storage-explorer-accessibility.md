---
title: Acessibilidade do Explorador de Armazenamento Azure (Pré-visualização) / Microsoft Docs
description: Acessibilidade do Explorador de Armazenamento Azure (Pré-visualização)
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: aa42d6b898a3cc474575f6c8dae65ff420fc8e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60457271"
---
# <a name="storage-explorer-accessibility"></a>Acessibilidade do Explorador de Armazenamento

## <a name="screen-readers"></a>Leitores de Tela

O Storage Explorer suporta a utilização de um leitor de ecrãs no Windows e Mac. Recomenda-se aos leitores de ecrã seguintes para cada plataforma:

Plataforma | Leitor de ecrã
---------|--------------
Windows  | NVDA
Mac      | Voice Over
Linux    | (os leitores de ecrã não são suportados no Linux)

Se tiver um problema de acessibilidade ao utilizar o Storage Explorer, por favor [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom

Pode tornar o texto no Storage Explorer maior através do zooming. Para ampliar, clique em **Zoom In** no menu Ajuda. Também pode utilizar o menu Ajuda para ampliar e redefinir o nível de zoom para o nível padrão.

![Opções de zoom no menu de ajuda][0]

A regulação de zoom aumenta o tamanho da maioria dos elementos ui. Recomenda-se também ativar grandes configurações de texto e zoom para o seu SISTEMA para garantir que todos os elementos uI estão devidamente dimensionados.

## <a name="high-contrast-themes"></a>Temas de Alto Contraste

O Storage Explorer tem dois temas de alto contraste, **Luz de Alto Contraste** e Alta Contraste **Escuro.** Pode alterar o seu tema selecionando no menu Ajuda > Temas.

![Menu sub temático de temas][1]

A definição temática muda a cor da maioria dos elementos ui. Recomenda-se também ativar o tema de alto contraste correspondente do seu OS para garantir que todos os elementos ui estão devidamente coloridos.

## <a name="shortcut-keys"></a>Teclas de atalho

### <a name="window-commands"></a>Comandos da Janela

Comando       | Atalho de teclado
--------------|--------------------
Nova Janela    | **Controlo+Shift+N**
Editor Próximo  | **Controlo+F4**
Saída          | **Controlo+Shift+W**

### <a name="navigation-commands"></a>Comandos de Navegação

Comando                | Atalho de teclado
-----------------------|----------------------
Foco Próximo Painel       | **F6**
Painel anterior de foco   | **Turno+F6**
Explorador               | **Control+Shift+E**
Gestão de Contas     | **Control+Shift+A**
Barra lateral de toggle        | **Controlo+B**
Registo de Atividades           | **Control+Shift+L**
Ações e Propriedades | **Controlo+Shift+P**
Editor Atual         | **Control+Home**
Próximo Editor            | **Control+Page Down**
Editor Anterior        | **Control+Page Up**

### <a name="zoom-commands"></a>Comandos de Zoom

Comando  | Atalho de teclado
---------|------------------
Zoom dentro  | **Controlo+=**
Zoom out | **Controlo+-**

### <a name="blob-and-file-share-editor-commands"></a>Comandos do Editor blob e file share

Comando | Atalho de teclado
--------|--------------------
Anterior    | **Seta alt+esquerda**
a frente | **Seta alt+direita**
Cima      | **Seta Alt+Up**

### <a name="editor-commands"></a>Comandos do Editor

Comando | Atalho de teclado
--------|------------------
Copiar    | **Controlo+C**
Cortar     | **Controlo+X**
Colar   | **Controlo+V**
Atualizar  | **Controlo+R**

### <a name="other-commands"></a>Outros Comandos

Comando                | Atalho de teclado
-----------------------|------------------
Ferramentas de desenvolvimento de alternância | **F12**
Recarregar                 | **Alt+Control+R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
