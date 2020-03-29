---
title: Implementar associações usando políticas
description: Aprenda sobre a implementação de associações para um fornecedor personalizado usando o serviço De Política Azure.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650477"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Implementar associações para um fornecedor personalizado usando a política azure

As políticas do Azure podem ser usadas para implantar associações para associar recursos a um fornecedor personalizado. Neste artigo, descrevemos uma política incorporada que implementa associações e como pode utilizar essa política.

## <a name="built-in-policy-to-deploy-associations"></a>Política incorporada para implantar associações

Implementar associações para um fornecedor personalizado é uma política incorporada que pode ser usada para implementar associação para associar um recurso a um fornecedor personalizado. A política aceita três parâmetros:

- ID do fornecedor personalizado - Este ID é o ID de recursos do fornecedor personalizado ao qual os recursos precisam de ser associados.
- Tipos de recursos para associados - Estes tipos de recursos são a lista de tipos de recursos a associar ao fornecedor personalizado. Pode associar vários tipos de recursos a um fornecedor personalizado usando a mesma política.
- Prefixo de nome de associação - Esta cadeia é o prefixo a ser adicionado ao nome do recurso de associação que está a ser criado. O valor predefinido é "DeployedByPolicy".

A política utiliza a avaliação DeployIfNotExists. Funciona depois de um Fornecedor de Recursos ter tratado de um pedido de recurso de criação ou atualização e a avaliação devolveu um código de estado de sucesso. Depois disso, o recurso da associação é implantado usando uma implementação do modelo.
Para mais informações sobre associações, consulte [o recurso azure Custom Providers onboarding](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Como usar as associações de implantação política incorporada 

### <a name="prerequisites"></a>Pré-requisitos
Se o fornecedor personalizado precisar de permissões ao âmbito da política para realizar uma ação, a implementação de recursos de associação não funcionaria sem conceder as permissões.

### <a name="policy-assignment"></a>Atribuição de política
Para utilizar a política incorporada, crie uma atribuição de políticas e atribua as associações Deploy para uma política de fornecedor personalizado. A política identificará então recursos não conformes e mobilizará a associação para esses recursos.

![Atribuir política incorporada](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Obter ajuda

Se tiver dúvidas sobre o desenvolvimento de Fornecedores de Recursos Personalizados Azure, tente perguntar-lhes sobre [o Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido respondida, por isso verifique primeiro antes de publicar. Adicione a ```azure-custom-providers``` etiqueta para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a política incorporada para implementar associações. Veja estes artigos para saber mais:

- [Conceitos: Recursos dos Fornecedores Personalizados Azure no embarque](./concepts-resource-onboarding.md)
- [Tutorial: Recurso a bordo com fornecedores personalizados](./tutorial-resource-onboarding.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Quickstart: Criar um fornecedor de recursos personalizados e implementar recursos personalizados](./create-custom-provider.md)
- [Como: Adicionar ações personalizadas a uma API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados a uma API Azure REST](./custom-providers-resources-endpoint-how-to.md)
