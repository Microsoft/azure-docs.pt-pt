---
title: Criar ações personalizadas e os recursos no Azure
description: Este tutorial abordará como criar ações personalizadas e recursos no Azure Resource Manager e como integrá-las em fluxos de trabalho personalizados para modelos do Gestor de recursos do Azure, CLI do Azure, Azure Policy e registo de atividades.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800044"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Criar ações personalizadas e os recursos no Azure

Fornecedores personalizados permitem-lhe personalizar fluxos de trabalho no Azure. Um provedor personalizado é um contrato entre o Azure e um `endpoint`. Ele permite que a adição de novas APIs personalizadas no Azure Resource Manager para ativar a nova implementação e capacidades de gestão. Este tutorial irá percorrer um exemplo simples de como adicionar novas ações e recursos para o Azure e como integrá-las.

Este tutorial é dividido nos seguintes passos:

- Configurar as funções do Azure para fornecedores personalizados do Azure
- Criação de um ponto de extremidade RESTful para fornecedores personalizados
- Criar e utilizar o provedor personalizado

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Configurar as funções do Azure para fornecedores personalizados do Azure

Esta parte do tutorial irá entrar em detalhes sobre como configurar uma função do Azure para trabalhar com fornecedores personalizados. Fornecedores personalizados podem trabalhar com qualquer URL pública.

- [Configurar as funções do Azure para fornecedores personalizados do Azure](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Criação de um ponto de extremidade RESTful para fornecedores personalizados

Esta parte do tutorial irá entrar em detalhes sobre a criação de um ponto de extremidade RESTful para fornecedores personalizados.

- [Criação de um ponto de extremidade RESTful para fornecedores personalizados](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>Criar e utilizar o provedor personalizado

Esta parte do tutorial irá entrar em detalhes sobre como criar um provedor personalizado e usar seus recursos e ações personalizadas.

- [Criar e utilizar um fornecedor personalizado do Azure](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendemos sobre provedores personalizados e como criar uma. Para continuar com o passo seguinte do tutorial:

- [Tutorial: Configurar as funções do Azure para fornecedores personalizados do Azure](./tutorial-custom-providers-function-setup.md)

Se estiver à procura de referências ou um início rápido, eis alguns links úteis:

- [Quickstart: Criar o fornecedor de recursos personalizado do Azure e implementar recursos personalizados](./create-custom-provider.md)
- [How To: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [How To: Adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
