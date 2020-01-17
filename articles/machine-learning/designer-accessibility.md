---
title: Usar recursos de acessibilidade no designer (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os atalhos de teclado e os recursos de acessibilidade do leitor de tela disponíveis no designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 9c899ae16d16fc76f0ea8d24169451556cf24025
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76158016"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Usar um teclado para usar o designer de Azure Machine Learning (versão prévia)

Saiba como usar um teclado e um leitor de tela para usar Azure Machine Learning designer. Para obter uma lista de atalhos de teclado que funcionam em qualquer lugar na portal do Azure, consulte [atalhos de teclado no portal do Azure](../azure-portal/azure-portal-keyboard-shortcuts.md)

Este fluxo de trabalho foi testado com [o Narrator e o](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) [Jaws](https://www.freedomscientific.com/products/software/jaws/), mas ele deve funcionar com outros leitores de tela padrão.

## <a name="navigate-the-pipeline-graph"></a>Navegar pelo grafo de pipeline

O grafo de pipeline é organizado como uma lista de listas. A lista de módulos de nível superior contém todo o módulo no pipeline. Cada item na lista de módulos contém uma lista de conexões que descreve todas as suas conexões. 

1. Na lista módulo, use a tecla de direção para alternar os módulos.
1. Use Tab para abrir a lista de conexões do módulo de destino.
1. Use a tecla de direção para alternar entre as portas de conexão do módulo.
1. Use "G" para ir para o módulo de destino.

## <a name="edit-the-pipeline-graph"></a>Editar o grafo do pipeline

### <a name="add-a-module-to-the-graph"></a>Adicionar um módulo ao grafo

1. Use Ctrl + F6 para alternar o foco da tela para a árvore de módulos.
1. Localize o módulo desejado na árvore de módulo usando o controle TreeView padrão.

### <a name="edit-a-module"></a>Editar um módulo

Para conectar um módulo a outro módulo:

1. Use Ctrl + Shift + H ao direcionar para um módulo na lista de módulos para abrir o auxiliar de conexão.
1. Edite as portas de conexão do módulo.

Para ajustar as propriedades do módulo:

1. Use Ctrl + Shift + E ao direcionar um módulo para abrir as propriedades do módulo.
1. Edite as propriedades do módulo.

## <a name="navigation-shortcuts"></a>Atalhos de navegação

| Teclas | Descrição |
|-|-|
| Ctrl + F6 | Alternar o foco entre a árvore de tela e o módulo |
| CTRL + F1   | Abrir o cartão de informações ao se concentrar em um nó na árvore de módulo |
| Ctrl + Shift + H | Abrir o auxiliar de conexão quando o foco estiver em um nó |
| Ctrl + Shift + E | Abrir propriedades do módulo quando o foco estiver em um nó |
| Ctrl + G | Mover o foco para o primeiro nó com falha se a execução do pipeline falhar |

## <a name="action-shortcuts"></a>Atalhos de ação

Use os atalhos a seguir com a chave de acesso. Para obter mais informações sobre chaves de acesso, consulte https://en.wikipedia.org/wiki/Access_key.

| Teclas | Ação |
|-|-|
| Tecla de acesso + R | Executar |
| Tecla de acesso + P | Publicar |
| Tecla de acesso + C | Clone |
| Tecla de acesso + D | Implementar |
| Tecla de acesso + I | Criar/atualizar pipeline de inferência |
| Tecla de acesso + B | Criar/atualizar pipeline de inferência de lote |
| Tecla de acesso + K | Abra a lista suspensa "criar pipeline de inferência" |
| Tecla de acesso + U | Abrir o menu suspenso "atualizar o pipeline de inferência" |
| Tecla de acesso + M | Abrir mais (...) DropDown |

## <a name="next-steps"></a>Passos seguintes

- [Ativar o alto contraste ou a alteração de tema](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Ferramentas relacionadas à acessibilidade na Microsoft](https://www.microsoft.com/accessibility)
