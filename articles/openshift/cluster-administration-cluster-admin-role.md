---
title: Papel de administrador de cluster Azure Red Hat OpenShift | Microsoft Docs
description: Atribuição e utilização do papel de administrador de cluster Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636259"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Papel de administrador de cliente azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 será retirado a 30 de junho de 2022. O apoio à criação de novos clusters Azure Red Hat OpenShift 3.11 continua até 30 de novembro de 2020. Após a reforma, os restantes clusters Azure Red Hat OpenShift 3.11 serão encerrados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se tiver questões específicas, [contacte-nos.](mailto:arofeedback@microsoft.com)

És o administrador de um aglomerado de chapéus vermelhos Azure. A sua conta aumentou as permissões e o acesso a todos os projetos criados pelo utilizador.

Quando a sua conta tem a função de autorização de cluster de administração do cliente vinculada a ela, pode gerir automaticamente um projeto.

> [!Note] 
> O papel do cluster de agrupamento de administração do cliente não é o mesmo que o papel de cluster-administrador.

Por exemplo, pode executar ações associadas a um conjunto de verbos ( `create` ) para operar num conjunto de nomes de recursos `templates` (). Para ver os detalhes destas funções e os seus conjuntos de verbos e recursos, executar o seguinte comando:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Os nomes dos verbos não são necessariamente todos mapas diretamente para `oc` comandos. Comparam-se mais genericamente aos tipos de operações CLI que podes realizar. 

Por exemplo, ter o `list` verbo significa que pode apresentar uma lista de todos os objetos de um nome de recurso ( `oc get` ). O `get` verbo significa que pode apresentar os detalhes de um objeto específico se souber o seu nome ( `oc describe` ).

## <a name="configure-the-customer-administrator-role"></a>Configure a função de administrador do cliente

Só pode configurar o papel de cluster de agrupamento de administração do cliente durante a criação do cluster, fornecendo a bandeira `--customer-admin-group-id` . Este campo não é atualmente configurável no portal Azure. Para aprender a configurar o Azure Ative Directory e o grupo de Administradores, consulte a [integração do Azure Ative Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Confirme a adesão à função de administrador de clientes

Para confirmar a sua adesão ao grupo de administração de clientes, experimente os comandos OpenShift CLI `oc get nodes` ou `oc projects` . `oc get nodes` apresentará uma lista de nós se tiver a função de cluster de administração do cliente e um erro de permissão se tiver apenas a função de projeto de administração do cliente. `oc projects` mostrará todos os projetos no cluster em oposição apenas aos projetos em que está a trabalhar.

Para explorar ainda mais papéis e permissões no seu cluster, pode utilizar o [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) comando.

## <a name="next-steps"></a>Passos seguintes

Configure o papel de cluster de agrupamento de administração do cliente:
> [!div class="nextstepaction"]
> [Integração do Azure Ative Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md)
