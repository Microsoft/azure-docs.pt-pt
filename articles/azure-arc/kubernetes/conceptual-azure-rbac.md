---
title: Azure RBAC - Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral conceptual da capacidade do Azure RBAC em Azure Arc habilitado a Kubernetes
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451102"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure RBAC em Azure Arc permitiu kubernetes

Os tipos de objetos De [ClusterRoleBinding e RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) ajudam a definir a autorização em Kubernetes de forma nativa. Com o Azure RBAC, pode utilizar o Azure Ative Directory (Azure AD) e atribuições de funções em Azure para controlar os controlos de autorização no cluster.

Com esta funcionalidade, todos os benefícios das atribuições de funções Azure, tais como registos de atividade que mostram todas as alterações do Azure RBAC a um recurso Azure, passam a ser aplicáveis para o seu cluster Kubernetes habilitado a Azure Arc.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Arquitetura - Azure RBAC em Azure Arc habilitado Kubernetes

[![Arquitetura Azure RBAC ](./media/conceptual-azure-rbac.png)](./media/conceptual-azure-rbac.png#lightbox)

Para encaminhar todas as verificações de acesso à autorização para o serviço de autorização em Azure, um servidor webhook[(guard](https://github.com/appscode/guard)) é implantado no cluster.

O `apiserver` cluster está configurado para usar a autenticação de [token webhook](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) e [a autorização webhook](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) para que e os `TokenAccessReview` `SubjectAccessReview` pedidos sejam encaminhados para o servidor webhook da guarda. Os `TokenAccessReview` `SubjectAccessReview` pedidos e pedidos são desencadeados por pedidos de recursos Kubernetes enviados para o `apiserver` .

A Guard faz então uma `checkAccess` chamada para o serviço de autorização em Azure para ver se a entidade AD AZure solicitada tem acesso ao recurso de preocupação. 

Se existir uma função de atribuição que permita este acesso, então `allowed` uma resposta é enviada do guarda de serviço de autorização. A Guarda, por sua vez, envia uma `allowed` resposta `apiserver` ao, permitindo à entidade de chamadas aceder ao recurso Kubernetes solicitado.


Se não existir uma função na atribuição que permita este acesso, então `denied` uma resposta é enviada do serviço de autorização para guardar. A Guarda envia uma `denied` resposta à entidade de chamadas um erro `apiserver` 403 proibido no recurso solicitado.

## <a name="next-steps"></a>Passos seguintes

* Use o nosso quickstart para [ligar um cluster Kubernetes ao Arco Azure.](./quickstart-connect-cluster.md)
* [Configurar o Azure RBAC](./azure-rbac.md) no seu Azure Arc ativado cluster Kubernetes.