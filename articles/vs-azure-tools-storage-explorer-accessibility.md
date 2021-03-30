---
title: Azure Storage Explorer Accessibility | Microsoft Docs
description: Compreender a acessibilidade no Azure Storage Explorer. Reveja quais os leitores de ecrã disponíveis, a capacidade de zoom, temas de alto contraste e teclas de atalho.
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
ms.openlocfilehash: ca4a8d719277eaa1d853d53d282649f839256be9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88035490"
---
# <a name="storage-explorer-accessibility"></a>Acessibilidade do Explorador de Armazenamento

## <a name="screen-readers"></a>Leitores de tela

O Storage Explorer suporta a utilização de um leitor de ecrã no Windows e mac. Recomenda-se para cada plataforma os seguintes leitores de ecrã:

Plataforma | Leitor de tela
---------|--------------
Windows  | NVDA
Mac      | Voice Over
Linux    | (os leitores de ecrã não são suportados no Linux)

Se tiver um problema de acessibilidade ao utilizar o Storage Explorer, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom

Pode tornar o texto no Storage Explorer maior através do zooming. Para ampliar, clique no **Zoom In** no menu Ajuda. Também pode utilizar o menu Ajuda para ampliar e redefinir o nível de zoom de volta ao nível predefinido.

![Opções de zoom no menu de ajuda][0]

A definição de zoom aumenta o tamanho da maioria dos elementos de UI. Recomenda-se também ativar grandes definições de texto e zoom para o seu SO para garantir que todos os elementos de UI estão devidamente dimensionados.

## <a name="high-contrast-themes"></a>Temas de alto contraste

O Storage Explorer tem dois temas de alto contraste, **High Contrast Light** e High Contrast **Dark.** Pode alterar o seu tema selecionando no menu "Temas > Ajuda".

![Menu sub temas][1]

A definição do tema altera a cor da maioria dos elementos de UI. Recomenda-se também ativar o tema de alto contraste correspondente do seu SO para garantir que todos os elementos de UI estão devidamente coloridos.

## <a name="shortcut-keys"></a>Chaves de atalho

### <a name="window-commands"></a>Comandos de janela

Comando       | Atalho de teclado
--------------|--------------------
Nova Janela    | **Control+Shift+N**
Editor Próximo  | **Controlo+F4**
Saia          | **Control+Shift+W**

### <a name="navigation-commands"></a>Comandos de Navegação

Comando                | Atalho de teclado
-----------------------|----------------------
Foco Próximo Painel       | **F6**
Foco Painel Anterior   | **Shift+F6**
Explorador               | **Control+Shift+E**
Gestão de Contas     | **Control+Shift+A**
Barra lateral toggle        | **Controlo+B**
Registo de Atividades           | **Control+Shift+L**
Ações e Propriedades | **Control+Shift+P**
Editor atual         | **Control+Home**
Próximo Editor            | **Control+Page Down**
Editor anterior        | **Control+Page Up**

### <a name="zoom-commands"></a>Comandos zoom

Comando  | Atalho de teclado
---------|------------------
Zoom In  | **Controlo+=**
Zoom out | **Controlo+-**

### <a name="blob-and-file-share-editor-commands"></a>Comandos editores de blob e de partilha de ficheiros

Comando | Atalho de teclado
--------|--------------------
Voltar    | **Seta Alt+Esquerda**
a frente | **Seta Alt+Direita**
Cima      | **Seta Alt+Up**

### <a name="editor-commands"></a>Comandos editoriais

Comando | Atalho de teclado
--------|------------------
Copiar    | **Controlo+C**
Cortar     | **Control+X**
Colar   | **Controlo+V**
Atualizar  | **Controlo+R**

### <a name="other-commands"></a>Outros Comandos

Comando                | Atalho de teclado
-----------------------|------------------
Ferramentas de desenvolvedor toggle | **F12**
Recarregar                 | **Alt+Control+R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
