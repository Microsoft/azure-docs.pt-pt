---
title: Implementar associações para aplicação gerida usando a política
description: Saiba como implementar associações para uma aplicação gerida utilizando o serviço Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650945"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Implementar associações para uma aplicação gerida usando a Política Azure

As políticas Azure podem ser usadas para implantar associações para associar recursos a uma aplicação gerida. Neste artigo, descrevemos uma política incorporada que implementa associações e como pode usar essa política.

## <a name="built-in-policy-to-deploy-associations"></a>Política incorporada para implantar associações

Implementar associações para uma aplicação gerida é uma política incorporada que pode ser usada para implantar associação para associar um recurso a uma aplicação gerida. A política aceita três parâmetros:

- ID de aplicação gerido - Este ID é o ID de recursos da aplicação gerida à qual os recursos precisam de ser associados.
- Tipos de recursos para associar - Estes tipos de recursos são a lista de tipos de recursos a associar à aplicação gerida. Pode associar vários tipos de recursos a uma aplicação gerida utilizando a mesma política.
- Prefixo de nome de associação - Esta cadeia é o prefixo a ser adicionado ao nome do recurso de associação que está a ser criado. O valor predefinido é "Implementado Por Política".

A política utiliza a avaliação do DeployIfNotExists. Funciona depois de um Fornecedor de Recursos ter tratado um pedido de criação ou atualização de recursos do tipo de recursos selecionados e a avaliação devolveu um código de estado de sucesso. Depois disso, o recurso de associação é implantado usando uma implementação de modelo.
Para obter mais informações sobre associações, consulte [o recurso Azure Custom Providers no embarque](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Como usar a política de implantação de associações incorporadas 

### <a name="prerequisites"></a>Pré-requisitos
Se a aplicação gerida necessitar de permissões para a subscrição para realizar uma ação, a implementação da política de recursos de associação não funcionaria sem conceder as permissões.

### <a name="policy-assignment"></a>Atribuição de políticas
Para utilizar a política incorporada, crie uma atribuição de política e atribua as associações Deploy para uma política de aplicação gerida. Uma vez atribuída a política com êxito, a política identificará recursos não conformes e mobilizará a associação para esses recursos.

![Atribuir a política incorporada](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Obter ajuda

Se tiver dúvidas sobre o desenvolvimento de Fornecedores de Recursos Personalizados Azure, tente fazê-los no [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Uma pergunta semelhante pode já ter sido respondida, por isso verifique primeiro antes de publicar. Adicione a etiqueta ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a política incorporada para implementar associações. Consulte estes artigos para saber mais:

- [Conceitos: Azure Custom Providers recurso onboarding](../custom-providers/concepts-resource-onboarding.md)
- [Tutorial: Embarque de recursos com fornecedores personalizados](../custom-providers/tutorial-resource-onboarding.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Quickstart: Criar um fornecedor de recursos personalizado e implementar recursos personalizados](../custom-providers/create-custom-provider.md)
- [Como: Adicionar ações personalizadas a uma API Azure REST](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados a uma API Azure REST](../custom-providers/custom-providers-resources-endpoint-how-to.md)
