---
title: Com o editor do Azure Cloud Shell | Documentos da Microsoft
description: Descrição geral de como usar o editor de Azure Cloud Shell.
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
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243164"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Com o editor do Azure Cloud Shell

O Azure Cloud Shell inclui um editor de integrada de arquivos criado a partir de código aberto [Monaco Editor](https://github.com/Microsoft/monaco-editor). O editor do Cloud Shell oferece suporte a recursos como o realce de linguagem, a paleta de comandos e um Gerenciador de arquivos.

![Editor do cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Ao abrir o editor

Para a criação do arquivo simples e a edição, iniciar o editor ao executar `code .` no terminal Cloud Shell. Esta ação abre o editor com o active directory de trabalho definido no terminal.

Para abrir diretamente um arquivo para edição rápida, execute `code <filename>` para abrir o editor sem o Explorador de ficheiros.

Para abrir o editor através do botão de interface do Usuário, clique o `{}` editor ícone da barra de ferramentas. Esta ação irá abrir o editor e predefinido do Explorador de ficheiros para o `/home/<user>` diretório.

## <a name="closing-the-editor"></a>Fechar o editor

Para fechar o editor, abra a `...` painel de ação no canto superior direito do editor e selecione `Close editor`.

![Feche o editor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta de comandos

Para iniciar a paleta de comandos, utilize o `F1` quando o foco está definido no editor da chave. Abrir a paleta de comandos também pode ser feito por meio do painel de ação.

![Cmd paleta](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Contribuir para o Editor Monaco

O suporte de realce de linguagem no editor do Cloud Shell é suportado por meio da funcionalidade a montante no [Monaco Editor](https://github.com/Microsoft/monaco-editor)utilização de definições de sintaxe Monarch. Para saber como fazer contribuições, leia os [Guia do contribuinte Mónaco](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Passos Seguintes
[Experimente o início rápido para o Bash no Cloud Shell](quickstart.md)
[ver a lista completa de ferramentas integradas do Cloud Shell](features.md)