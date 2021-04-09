---
title: Usando o editor da Azure Cloud Shell | Microsoft Docs
description: Visão geral de como usar o editor da Azure Cloud Shell.
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
ms.openlocfilehash: 1e3ea9222b0f231250bde43fb86c07847ca4835e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832340"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Usando o editor da Azure Cloud Shell

A Azure Cloud Shell inclui um editor de ficheiros integrado construído a partir do editor de código aberto [do Mónaco.](https://github.com/Microsoft/monaco-editor) O editor do Cloud Shell suporta funcionalidades como o realce da linguagem, a paleta de comandos e um explorador de ficheiros.

![Editor da Cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Abertura do editor

Para criação e edição de ficheiros simples, inicie o editor ao executar `code .` no terminal do Cloud Shell. Esta ação abre o editor com o diretório de trabalho ativo definido no terminal.

Para abrir diretamente um ficheiro para edição rápida, execute `code <filename>` para abrir o editor sem o explorador de ficheiros.

Para abrir o editor através do botão da IU, clique no ícone do editor `{}` a partir da barra de ferramentas. Isto irá abrir o editor e predefinir o explorador de ficheiros no diretório `/home/<user>`.

## <a name="closing-the-editor"></a>Fechar o editor

Para fechar o editor, abra o `...` painel de ação no topo direito do editor e selecione `Close editor` .

![Editor próximo](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta de comando

Para lançar a paleta de comando, utilize a `F1` chave quando o foco estiver definido no editor. A abertura da paleta de comando também pode ser feita através do painel de ação.

![Paleta de cmd](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Contribuindo para o Editor do Mónaco

O suporte de destaque linguístico no editor cloud Shell é suportado através da funcionalidade a montante na utilização do [Mónaco Editor](https://github.com/Microsoft/monaco-editor)das definições de sintaxe Monarch. Para aprender a fazer contribuições, leia o guia de colaboradores do [Mónaco.](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)

## <a name="next-steps"></a>Passos seguintes

- [Experimente o quickstart para Bash em Cloud Shell](quickstart.md)
- [Veja a lista completa de ferramentas cloud shell integradas](features.md)
