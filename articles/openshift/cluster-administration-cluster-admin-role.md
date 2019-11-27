---
title: Função de administrador de cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Atribuição e uso da função de administrador de cluster do Red Hat OpenShift do Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 0cb875122c63be18f7c39cdfea7986d705ed434e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539264"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Função de administrador de clientes do Red Hat OpenShift do Azure

Você é o administrador de cluster de um cluster do Azure Red Hat OpenShift. Sua conta aumentou as permissões e o acesso a todos os projetos criados pelo usuário.

Quando sua conta tiver a função de autorização Customer-admin-cluster associada a ela, ela poderá gerenciar automaticamente um projeto.

> [!Note] 
> A função de cluster Customer-admin-cluster não é a mesma que a função de cluster de administrador de cluster.


Por exemplo, você pode executar ações associadas a um conjunto de verbos (`create`) para operar em um conjunto de nomes de recursos (`templates`). Para exibir os detalhes dessas funções e seus conjuntos de verbos e recursos, execute o seguinte comando:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Os nomes de verbo não necessariamente são mapeados diretamente para `oc` comandos. Eles correspondem mais geralmente aos tipos de operações da CLI que você pode executar. 

Por exemplo, ter o `list` verbo significa que você pode exibir uma lista de todos os objetos de um nome de recurso (`oc get`). O verbo `get` significa que você pode exibir os detalhes de um objeto específico se souber seu nome (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Configurar a função de administrador do cliente

Você pode configurar a função de cluster Customer-admin-cluster somente durante a criação do cluster, fornecendo o sinalizador `--customer-admin-group-id`. Para saber como configurar Azure Active Directory e o grupo de administradores, confira [integração Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Passos Seguintes

Configure a função de cluster Customer-admin-cluster:
> [!div class="nextstepaction"]
> [Integração do Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md)
