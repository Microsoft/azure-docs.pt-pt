---
title: Função de administrador de cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Atribuição e uso da função de administrador de cluster do Red Hat OpenShift do Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 610b1e0112b8135aa09ade5c800eaed987635cb4
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545641"
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

Você pode configurar a função de cluster Customer-admin-cluster somente durante a criação do cluster, fornecendo o sinalizador `--customer-admin-group-id`. Este campo não é configurável no momento no portal do Azure. Para saber como configurar Azure Active Directory e o grupo de administradores, confira [integração Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Confirmar associação na função de administrador do cliente

Para confirmar sua associação no grupo administrador do cliente, experimente os comandos da CLI do OpenShift `oc get nodes` ou `oc projects`. `oc get nodes` mostrará uma lista de nós se você tiver a função Customer-admin-cluster e um erro de permissão se você tiver apenas a função Customer-admin-Project. `oc projects` mostrará todos os projetos no cluster em vez de apenas os projetos nos quais você está trabalhando.

Para explorar ainda mais as funções e permissões no cluster, você pode usar o comando [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) .

## <a name="next-steps"></a>Passos seguintes

Configure a função de cluster Customer-admin-cluster:
> [!div class="nextstepaction"]
> [Integração do Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md)
