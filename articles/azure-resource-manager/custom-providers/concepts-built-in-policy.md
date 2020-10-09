---
title: Implementar associações usando políticas
description: Saiba como implementar associações para um fornecedor personalizado utilizando o serviço Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 00cd1d39c0110aac9ea96f73127e83197976c95a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82190134"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Implementar associações para um fornecedor personalizado que utilize a Política Azure

As políticas Azure podem ser usadas para implantar associações para associar recursos a um fornecedor personalizado. Neste artigo, descrevemos uma política incorporada que implementa associações e como pode usar essa política.

## <a name="built-in-policy-to-deploy-associations"></a>Política incorporada para implantar associações

Implementar associações para um fornecedor personalizado é uma política incorporada que pode ser usada para implementar associação para associar um recurso a um fornecedor personalizado. A política aceita três parâmetros:

- ID do fornecedor personalizado - Este ID é o ID de recursos do fornecedor personalizado ao qual os recursos precisam de ser associados.
- Tipos de recursos para associar - Estes tipos de recursos são a lista de tipos de recursos a associar ao fornecedor personalizado. Pode associar vários tipos de recursos a um fornecedor personalizado utilizando a mesma política.
- Prefixo de nome de associação - Esta cadeia é o prefixo a ser adicionado ao nome do recurso de associação que está a ser criado. O valor predefinido é "Implementado Por Política".

A política utiliza a avaliação do DeployIfNotExists. Funciona depois de um Fornecedor de Recursos ter tratado um pedido de criação ou atualização de recursos e a avaliação ter devolvido um código de estado de sucesso. Depois disso, o recurso de associação é implantado usando uma implementação de modelo.
Para obter mais informações sobre associações, consulte [o recurso Azure Custom Providers no embarque](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Como usar a política de implantação de associações incorporadas 

### <a name="prerequisites"></a>Pré-requisitos
Se o fornecedor personalizado precisar de permissões para o âmbito da apólice para realizar uma ação, a implementação de políticas de recursos de associação não funcionaria sem conceder as permissões.

### <a name="policy-assignment"></a>Atribuição de políticas
Para utilizar a política incorporada, crie uma atribuição de política e atribua as associações Deploy para uma política de fornecedores personalizados. A política identificará então os recursos não conformes e mobilizará a associação para esses recursos.

![Atribuir a política incorporada](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Obter ajuda

Se tiver dúvidas sobre o desenvolvimento de Fornecedores de Recursos Personalizados Azure, tente fazê-los no [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Uma pergunta semelhante pode já ter sido respondida, por isso verifique primeiro antes de publicar. Adicione a etiqueta ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a política incorporada para implementar associações. Consulte estes artigos para saber mais:

- [Conceitos: Azure Custom Providers recurso onboarding](./concepts-resource-onboarding.md)
- [Tutorial: Embarque de recursos com fornecedores personalizados](./tutorial-resource-onboarding.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Quickstart: Criar um fornecedor de recursos personalizado e implementar recursos personalizados](./create-custom-provider.md)
- [Como: Adicionar ações personalizadas a uma API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados a uma API Azure REST](./custom-providers-resources-endpoint-how-to.md)
