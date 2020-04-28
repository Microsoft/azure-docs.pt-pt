---
title: Implementar associações para aplicação gerida utilizando a política
description: Conheça a implementação de associações para uma aplicação gerida utilizando o serviço De Política Azure.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75650945"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Implementar associações para uma aplicação gerida utilizando a Política Azure

As políticas do Azure podem ser usadas para implantar associações para associar recursos a uma aplicação gerida. Neste artigo, descrevemos uma política incorporada que implementa associações e como pode utilizar essa política.

## <a name="built-in-policy-to-deploy-associations"></a>Política incorporada para implantar associações

Implementar associações para uma aplicação gerida é uma política incorporada que pode ser usada para implementar associação para associar um recurso a uma aplicação gerida. A política aceita três parâmetros:

- ID de aplicação gerida - Este ID é o ID de recurso da aplicação gerida à qual os recursos precisam de ser associados.
- Tipos de recursos para associados - Estes tipos de recursos são a lista de tipos de recursos a associar à aplicação gerida. Pode associar vários tipos de recursos a uma aplicação gerida utilizando a mesma política.
- Prefixo de nome de associação - Esta cadeia é o prefixo a ser adicionado ao nome do recurso de associação que está a ser criado. O valor predefinido é "DeployedByPolicy".

A política utiliza a avaliação DeployIfNotExists. Funciona depois de um Fornecedor de Recursos ter tratado um pedido de recurso de criação ou atualização do tipo de recursos selecionados e a avaliação devolveu um código de estado de sucesso. Depois disso, o recurso da associação é implantado usando uma implementação do modelo.
Para mais informações sobre associações, consulte [o recurso azure Custom Providers onboarding](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Como usar as associações de implantação política incorporada 

### <a name="prerequisites"></a>Pré-requisitos
Se a aplicação gerida precisar de permissões para a subscrição para realizar uma ação, a implementação de recursos de associação não funcionaria sem conceder as permissões.

### <a name="policy-assignment"></a>Atribuição de política
Para utilizar a política incorporada, crie uma atribuição de políticas e atribua as associações deploy para uma política de aplicação gerida. Uma vez atribuída a política com êxito, a política identificará recursos não conformes e implementará uma associação para esses recursos.

![Atribuir política incorporada](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Obter ajuda

Se tiver dúvidas sobre o desenvolvimento de Fornecedores de Recursos Personalizados Azure, tente perguntar-lhes sobre [o Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido respondida, por isso verifique primeiro antes de publicar. Adicione a ```azure-custom-providers``` etiqueta para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a política incorporada para implementar associações. Veja estes artigos para saber mais:

- [Conceitos: Recursos dos Fornecedores Personalizados Azure no embarque](../custom-providers/concepts-resource-onboarding.md)
- [Tutorial: Recurso a bordo com fornecedores personalizados](../custom-providers/tutorial-resource-onboarding.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Quickstart: Criar um fornecedor de recursos personalizados e implementar recursos personalizados](../custom-providers/create-custom-provider.md)
- [Como: Adicionar ações personalizadas a uma API Azure REST](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados a uma API Azure REST](../custom-providers/custom-providers-resources-endpoint-how-to.md)
