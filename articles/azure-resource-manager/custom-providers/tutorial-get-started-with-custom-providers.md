---
title: Criar as ações e os recursos personalizados
description: Este tutorial passa por cima de como criar ações e recursos personalizados no Azure Resource Manager. Também mostra como os fluxos de trabalho personalizados operam com modelos de gestor de recursos Azure, Azure CLI, Política Azure e Registo de Atividades Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649898"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Criar ações e recursos personalizados em Azure

Um fornecedor personalizado é um contrato entre o Azure e um ponto final. Com fornecedores personalizados, pode alterar fluxos de trabalho em Azure adicionando novas APIs ao Gestor de Recursos Azure. Com estas APIs personalizadas, o Gestor de Recursos pode usar novas capacidades de implementação e gestão.

Este tutorial passa por um simples exemplo de como adicionar novas ações e recursos ao Azure e como integrá-los.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurar funções Azure para fornecedores personalizados Azure

A primeira parte deste tutorial descreve como configurar uma aplicação de função Azure para trabalhar com fornecedores personalizados:

- [Configurar funções Azure para fornecedores personalizados Azure](./tutorial-custom-providers-function-setup.md)

Os fornecedores personalizados podem trabalhar com qualquer URL público.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Autor de um ponto final RESTful para fornecedores personalizados

A segunda parte deste tutorial descreve como autoria um ponto final RESTful para fornecedores personalizados:

- [Autoria um ponto final RESTful para fornecedores personalizados](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Criar e usar um fornecedor personalizado

A terceira parte deste tutorial descreve como criar um fornecedor personalizado e usar as suas ações e recursos personalizados:

- [Criar e usar um fornecedor personalizado](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre fornecedores personalizados e como construir um. Para continuar até ao próximo tutorial, consulte [Tutorial: Configurar funções Azure para fornecedores personalizados Azure](./tutorial-custom-providers-function-setup.md).

Se procura referências ou um arranque rápido, aqui estão alguns links úteis:

- [Quickstart: Criar um fornecedor de recursos personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Como: Adicionar ações personalizadas à API Do REST Azure](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados à API Do REST Azure](./custom-providers-resources-endpoint-how-to.md)
