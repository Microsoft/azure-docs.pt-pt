---
title: Utilize funcionalidades de acessibilidade no designer
titleSuffix: Azure Machine Learning
description: Conheça os atalhos de teclado e as funcionalidades de acessibilidade do leitor de ecrã disponíveis no designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: 86cb5260a59f864658fbb7ac1c1da2d943c6253e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90893431"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Use um teclado para usar o designer de aprendizagem de máquinas Azure

Aprenda a usar um leitor de teclado e ecrã para usar o designer Azure Machine Learning. Para obter uma lista de atalhos de teclado que funcionam em todo o lado no portal Azure, consulte [atalhos de teclado no portal Azure](../azure-portal/azure-portal-keyboard-shortcuts.md)

Este fluxo de trabalho foi testado com [o Narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) e [o JAWS,](https://www.freedomscientific.com/products/software/jaws/)mas deve funcionar com outros leitores de ecrã padrão.

## <a name="navigate-the-pipeline-graph"></a>Navegue no gráfico do gasoduto

O gráfico do pipeline é organizado como uma lista aninhada. A lista exterior é uma lista de módulos, que descreve todos os módulos no gráfico do pipeline. A lista interna é uma lista de ligações, que descreve todas as ligações de um módulo específico.  

1. Na lista de módulos, utilize a tecla de seta para mudar os módulos.
1. Utilize o separador para abrir a lista de ligação para o módulo-alvo.
1. Utilize a tecla de seta para alternar entre as portas de ligação para o módulo.
1. Use "G" para ir ao módulo alvo.

## <a name="edit-the-pipeline-graph"></a>Editar o gráfico do pipeline

### <a name="add-a-module-to-the-graph"></a>Adicione um módulo ao gráfico

1. Utilize ctrl+F6 para mudar o foco da tela para a árvore do módulo.
1. Encontre o módulo desejado na árvore do módulo utilizando o controlo padrão da visão de árvore.

### <a name="edit-a-module"></a>Editar um módulo

Para ligar um módulo a outro módulo:

1. Utilize ctrl + Shift + H ao direcionar um módulo na lista de módulos para abrir o ajudante de ligação.
1. Edite as portas de ligação para o módulo.

Para ajustar as propriedades do módulo:

1. Utilize ctrl + Shift + E ao direcionar um módulo para abrir as propriedades do módulo.
1. Editar as propriedades do módulo.

## <a name="navigation-shortcuts"></a>Atalhos de navegação

| Toque de tecla | Description |
|-|-|
| Ctrl + F6 | Foco de alternância entre tela e árvore de módulo |
| Ctrl + F1   | Abra o cartão de informação ao concentrar-se num nó na árvore do módulo |
| Ctrl + Turno + H | Abra o ajudante de ligação quando o foco estiver num nó |
| Ctrl + Turno + E | Abra as propriedades do módulo quando o foco está num nó |
| Ctrl + G | Mova o foco para o primeiro nó falhado se o gasoduto falhar |

## <a name="action-shortcuts"></a>Atalhos de ação

Utilize os seguintes atalhos com a chave de acesso. Para obter mais informações sobre as teclas de acesso, https://en.wikipedia.org/wiki/Access_key consulte.

| Toque de tecla | Ação |
|-|-|
| Chave de acesso + R | Executar |
| Chave de acesso + P | Publicar |
| Chave de acesso + C | Clone |
| Chave de acesso + D | Implementar |
| Chave de acesso + I | Criar/atualizar o gasoduto de inferência |
| Chave de acesso + B | Criar/atualizar o gasoduto de inferência do lote |
| Chave de acesso + K | Abra o dropdown "Create inference pipeline" |
| Chave de acesso + U | Abra o dropdown "Update inference pipeline" |
| Chave de acesso + M | Abrir mais(...) dropdown |

## <a name="next-steps"></a>Passos seguintes

- [Ativar o alto contraste ou a alteração de tema](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Ferramentas relacionadas com acessibilidade na Microsoft](https://www.microsoft.com/accessibility)
