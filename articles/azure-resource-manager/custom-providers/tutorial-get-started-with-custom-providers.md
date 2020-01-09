---
title: Criar ações e recursos personalizados
description: Este tutorial sobe como criar ações e recursos personalizados no Azure Resource Manager. Ele também mostra como os fluxos de trabalho personalizados interoperam com modelos de Azure Resource Manager, CLI do Azure, Azure Policy e log de atividades do Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649898"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Criar ações e recursos personalizados no Azure

Um provedor personalizado é um contrato entre o Azure e um ponto de extremidade. Com provedores personalizados, você pode alterar os fluxos de trabalho no Azure adicionando novas APIs ao Azure Resource Manager. Com essas APIs personalizadas, o Resource Manager pode usar novos recursos de implantação e gerenciamento.

Este tutorial percorre um exemplo simples de como adicionar novas ações e recursos ao Azure e como integrá-los.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurar Azure Functions para provedores personalizados do Azure

A parte um deste tutorial descreve como configurar um aplicativo de funções do Azure para trabalhar com provedores personalizados:

- [Configurar Azure Functions para provedores personalizados do Azure](./tutorial-custom-providers-function-setup.md)

Os provedores personalizados podem trabalhar com qualquer URL pública.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Criar um ponto de extremidade RESTful para provedores personalizados

A parte dois deste tutorial descreve como criar um ponto de extremidade RESTful para provedores personalizados:

- [Criando um ponto de extremidade RESTful para provedores personalizados](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Criar e usar um provedor personalizado

A parte três deste tutorial descreve como criar um provedor personalizado e usar suas ações e recursos personalizados:

- [Criar e usar um provedor personalizado](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre provedores personalizados e como criar um. Para continuar no próximo tutorial, consulte [tutorial: configurar Azure Functions para provedores personalizados do Azure](./tutorial-custom-providers-function-setup.md).

Se você estiver procurando referências ou um início rápido, aqui estão alguns links úteis:

- [Início rápido: criar um provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Como adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
