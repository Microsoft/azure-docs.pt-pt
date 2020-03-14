---
title: Papel de administrador de cluster Azure Red Hat OpenShift  Microsoft Docs
description: Atribuição e utilização do papel de administrador de cluster OpenShift do Chapéu Vermelho Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139101"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Papel de administrador de cliente da Azure Red Hat OpenShift

És o administrador de um cluster do Azure Red Hat OpenShift. A sua conta aumentou as permissões e acesso a todos os projetos criados pelo utilizador.

Quando a sua conta tem a função de autorização de cliente-administrador-cluster vinculada a ela, pode gerir automaticamente um projeto.

> [!Note] 
> O papel de cluster cliente-administrador não é o mesmo que o papel de cluster-admin.

Por exemplo, pode executar ações associadas a um conjunto de verbos (`create`) para operar num conjunto de nomes de recursos (`templates`). Para ver os detalhes destas funções e seus conjuntos de verbos e recursos, executar o seguinte comando:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Os nomes verbos não mapeiam diretamente para `oc` comandos. Comparam-se mais genericamente aos tipos de operações CLI que pode realizar. 

Por exemplo, ter o verbo `list` significa que pode apresentar uma lista de todos os objetos de um nome de recurso (`oc get`). O verbo `get` significa que pode apresentar os detalhes de um objeto específico se souber o seu nome (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Configure a função de administrador de cliente

Só é possível configurar a função de cluster cliente-administrador apenas durante a criação do cluster, fornecendo a bandeira `--customer-admin-group-id`. Este campo não é atualmente configurável no portal Azure. Para aprender a configurar o Azure Ative Directory e o grupo administradores, consulte a [integração do Diretório Ativo Azure para o Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Confirme a adesão ao papel de administrador de clientes

Para confirmar a sua adesão ao grupo de administração de clientes, experimente os comandos CLI OpenShift `oc get nodes` ou `oc projects`. `oc get nodes` mostrará uma lista de nós se tiver a função de cluster cliente-administrador, e um erro de permissão se tiver apenas o papel de cliente-administrador-projeto. `oc projects` mostrará todos os projetos do cluster em oposição apenas aos projetos em que está a trabalhar.

Para explorar ainda mais papéis e permissões no seu cluster, pode usar o [comando`oc policy who-can <verb> <resource>`.](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings)

## <a name="next-steps"></a>Passos seguintes

Configure a função de cluster cliente-administrador:
> [!div class="nextstepaction"]
> [Integração do Diretório Ativo Azure para o Azure Red Hat OpenShift](howto-aad-app-configuration.md)
