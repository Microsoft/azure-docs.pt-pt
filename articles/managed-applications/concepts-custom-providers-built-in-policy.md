---
title: Implantar associações para o provedor personalizado do Azure usando a política
description: Saiba mais sobre como implantar associações para um provedor personalizado usando o serviço Azure Policy.
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 94f7128a473266824242f52a9c50f09b2c24f1d4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932648"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Implantar associações para um provedor personalizado usando a política do Azure

As políticas do Azure podem ser usadas para implantar associações para associar recursos a um provedor personalizado. Neste artigo, descrevemos uma política interna que implanta associações e como você pode usar essa política.

## <a name="built-in-policy-to-deploy-associations"></a>Política interna para implantar associações

Implantar associações para um provedor personalizado é uma política interna que pode ser usada para implantar a associação para associar um recurso a um provedor personalizado. A política aceita três parâmetros:

- ID do provedor personalizado-essa ID é a ID de recurso do provedor personalizado ao qual os recursos precisam ser associados.
- Tipos de recursos a serem associados-esses tipos de recursos são a lista de tipos de recursos a serem associados ao provedor personalizado. Você pode associar vários tipos de recursos a um provedor personalizado usando a mesma política.
- Prefixo do nome da Associação-esta cadeia de caracteres é o prefixo a ser adicionado ao nome do recurso de associação que está sendo criado. O valor padrão é "DeployedByPolicy".

A política usa a avaliação DeployIfNotExists. Ele é executado depois que um provedor de recursos manipula uma solicitação de criação ou atualização de recurso e a avaliação retornou um código de status de êxito. Depois disso, o recurso de associação é implantado usando uma implantação de modelo.
Para obter mais informações sobre associações, consulte [integração de recursos de provedores personalizados do Azure](./concepts-custom-providers-resourceonboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Como usar a política interna implantar associações 

### <a name="prerequisites"></a>Pré-requisitos
Se o provedor personalizado precisar de permissões para o escopo da política para executar uma ação, a implantação da política do recurso de associação não funcionaria sem conceder as permissões.

### <a name="policy-assignment"></a>Atribuição de política
Para usar a política interna, crie uma atribuição de política e atribua as associações de implantação para uma política de provedor personalizado. Em seguida, a política identificará recursos sem conformidade e implantará a associação para esses recursos.

![Atribuir política interna](media/builtin-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Obter ajuda

Se você tiver dúvidas sobre o desenvolvimento de provedores de recursos personalizados do Azure, tente solicitá-los em [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido respondida, portanto, verifique primeiro antes de postar. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprenderá a usar a política interna para implantar associações. Consulte estes artigos para saber mais:

- [Conceitos: integração de recursos de provedores personalizados do Azure](./concepts-custom-providers-resourceonboarding.md)
- [Tutorial: integração de recursos com provedores personalizados](./tutorial-custom-providers-resource-onboarding.md)
- [Tutorial: criar ações e recursos personalizados no Azure](./tutorial-custom-providers-101.md)
- [Início rápido: criar um provedor de recursos personalizado e implantar recursos personalizados](./create-custom-provider.md)
- [Como adicionar ações personalizadas a uma API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados a uma API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
