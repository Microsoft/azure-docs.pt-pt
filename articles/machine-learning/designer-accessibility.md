---
title: Utilize funcionalidades de acessibilidade no designer (pré-visualização)
titleSuffix: Azure Machine Learning
description: Conheça os atalhos de teclado e as funcionalidades de acessibilidade do leitor de ecrã disponíveis no designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77366192"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Utilize um teclado para utilizar o designer de machine learning Azure (pré-visualização)

Aprenda a usar um leitor de teclado e ecrã para utilizar o designer de Machine Learning Azure. Para uma lista de atalhos de teclado que funcionam em todo o lado no portal Azure, consulte [atalhos de teclado no portal Azure](../azure-portal/azure-portal-keyboard-shortcuts.md)

Este fluxo de trabalho foi testado com [o Narrador](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) e o [JAWS,](https://www.freedomscientific.com/products/software/jaws/)mas deve funcionar com outros leitores de ecrã padrão.

## <a name="navigate-the-pipeline-graph"></a>Navegue no gráfico do oleoduto

O gráfico do oleoduto é organizado como uma lista aninhada. A lista externa é uma lista de módulos, que descreve todos os módulos do gráfico do pipeline. A lista interna é uma lista de ligação, que descreve todas as ligações de um módulo específico.  

1. Na lista de módulos, utilize a tecla seta para mudar os módulos.
1. Utilize o separador para abrir a lista de ligação para o módulo alvo.
1. Utilize a tecla seta para alternar entre as portas de ligação para o módulo.
1. Utilize "G" para ir ao módulo alvo.

## <a name="edit-the-pipeline-graph"></a>Editar o gráfico do pipeline

### <a name="add-a-module-to-the-graph"></a>Adicione um módulo ao gráfico

1. Utilize ctrl+F6 para mudar o foco da tela para a árvore do módulo.
1. Encontre o módulo desejado na árvore do módulo utilizando o controlo padrão da visão da árvore.

### <a name="edit-a-module"></a>Editar um módulo

Para ligar um módulo a outro módulo:

1. Utilize ctrl + Shift + H ao direcionar um módulo na lista de módulos para abrir o ajudante de ligação.
1. Editar as portas de ligação para o módulo.

Para ajustar as propriedades do módulo:

1. Utilize ctrl + Shift + E ao direcionar um módulo para abrir as propriedades do módulo.
1. Editar as propriedades do módulo.

## <a name="navigation-shortcuts"></a>Atalhos de navegação

| Tecla | Descrição |
|-|-|
| Ctrl + F6 | Foco de alternância entre tela e árvore de módulo |
| Ctrl + F1   | Abra o cartão de informações quando se concentrar num nó na árvore do módulo |
| Ctrl + Turno + H | Abra o ajudante de ligação quando o foco estiver num nó |
| Ctrl + Turno + E | Abrir propriedades do módulo quando o foco está em um nó |
| Ctrl + G | Mova o foco para o primeiro nó falhado se o gasoduto falhar |

## <a name="action-shortcuts"></a>Atalhos de ação

Utilize os seguintes atalhos com a chave de acesso. Para obter mais informações sobre as teclas de acesso, consulte https://en.wikipedia.org/wiki/Access_key.

| Tecla | Ação |
|-|-|
| Chave de acesso + R | Executar |
| Chave de acesso + P | Publicar |
| Chave de acesso + C | Clone |
| Chave de acesso + D | Implementação |
| Chave de acesso + I | Criar/atualizar o gasoduto de inferência |
| Chave de acesso + B | Criar/atualizar o gasoduto de inferência do lote |
| Chave de acesso + K | Open "Create inference pipeline" dropdown |
| Chave de acesso + U | Abrir "Update inference pipeline" dropdown |
| Chave de acesso + M | Abrir mais(...) dropdown |

## <a name="next-steps"></a>Passos seguintes

- [Ativar o alto contraste ou a alteração de tema](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Ferramentas relacionadas com a acessibilidade na Microsoft](https://www.microsoft.com/accessibility)
