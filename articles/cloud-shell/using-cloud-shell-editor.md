---
title: Usando o editor azure Cloud Shell [ Microsoft Docs
description: Visão geral de como usar o editor azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60199235"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Usando o editor Azure Cloud Shell

A Azure Cloud Shell inclui um editor de ficheiros integrado construído a partir do Editor do [Mónaco](https://github.com/Microsoft/monaco-editor)de código aberto. O editor do Cloud Shell suporta funcionalidades como o realce da linguagem, a paleta de comandos e um explorador de ficheiros.

![Editor da Cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Abrir o editor

Para criação e edição de ficheiros simples, inicie o editor ao executar `code .` no terminal do Cloud Shell. Esta ação abre o editor com o diretório de trabalho ativo definido no terminal.

Para abrir diretamente um ficheiro para edição rápida, execute `code <filename>` para abrir o editor sem o explorador de ficheiros.

Para abrir o editor através do botão da IU, clique no ícone do editor `{}` a partir da barra de ferramentas. Isto irá abrir o editor e predefinir o explorador de ficheiros no diretório `/home/<user>`.

## <a name="closing-the-editor"></a>Fechar o editor

Para fechar o editor, abra o `...` painel de ação `Close editor`no canto superior direito do editor e selecione .

![Editor próximo](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta de comando

Para lançar a paleta `F1` de comando, utilize a tecla quando o foco estiver definido no editor. A abertura da paleta de comando também pode ser feita através do painel de ação.

![Paleta cmd](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Contribuindo para o Editor do Mónaco

O suporte de destaque da linguagem no editor da Cloud Shell é suportado através da funcionalidade a montante no uso do Editor do [Mónaco](https://github.com/Microsoft/monaco-editor)das definições de sintaxe da Monarch. Para aprender a fazer contribuições, leia o guia de contribuição do [Mónaco.](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)

## <a name="next-steps"></a>Passos seguintes
[Experimente o quickstart para Bash in Cloud Shell](quickstart.md)
[Veja a lista completa de ferramentas integradas](features.md) cloud Shell