---
title: Criar as ações e os recursos personalizados
description: Este tutorial é sobre como criar ações e recursos personalizados no Azure Resource Manager. Também mostra como os fluxos de trabalho personalizados se interoperam com modelos de gestor de recursos Azure, Azure CLI, Azure Policy e Azure Activity Log.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75649898"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Criar ações e recursos personalizados em Azure

Um fornecedor personalizado é um contrato entre a Azure e um ponto final. Com fornecedores personalizados, pode alterar fluxos de trabalho em Azure adicionando novas APIs no Azure Resource Manager. Com estas APIs personalizadas, o Gestor de Recursos pode utilizar novas capacidades de implementação e gestão.

Este tutorial passa por um exemplo simples de como adicionar novas ações e recursos ao Azure e como integrá-los.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurar funções Azure para fornecedores personalizados Azure

A primeira parte deste tutorial descreve como criar uma app de função Azure para trabalhar com fornecedores personalizados:

- [Configurar funções Azure para fornecedores personalizados Azure](./tutorial-custom-providers-function-setup.md)

Os fornecedores personalizados podem trabalhar com qualquer URL público.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Autor de um ponto final RESTful para fornecedores personalizados

A segunda parte deste tutorial descreve como autorizar um ponto final RESTful para fornecedores personalizados:

- [Autoria de um ponto final RESTful para fornecedores personalizados](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Criar e utilizar um fornecedor personalizado

A terceira parte deste tutorial descreve como criar um fornecedor personalizado e usar as suas ações e recursos personalizados:

- [Criar e utilizar um fornecedor personalizado](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre fornecedores personalizados e como construir um. Para continuar o próximo tutorial, consulte [Tutorial: Configurar Funções Azure para Fornecedores Personalizados Azure](./tutorial-custom-providers-function-setup.md).

Se procura referências ou um arranque rápido, aqui ficam alguns links úteis:

- [Quickstart: Criar um fornecedor de recursos personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Como: Adicionar ações personalizadas à Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados à Azure REST API](./custom-providers-resources-endpoint-how-to.md)
