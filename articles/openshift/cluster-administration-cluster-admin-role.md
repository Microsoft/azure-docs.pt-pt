---
title: Função de administrador de cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Atribuição e uso da função de administrador de cluster do Red Hat OpenShift do Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936936"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Função de administrador de clientes do Red Hat OpenShift do Azure

Você é o administrador de cluster de um cluster do Azure Red Hat OpenShift. Sua conta aumentou as permissões e o acesso a todos os projetos criados pelo usuário.

Quando sua conta tiver a função de autorização OSA-Customer-admin associada a ela, ela poderá gerenciar automaticamente um projeto.

> [!Note] 
> A função de cluster OSA-Customer-admin não é a mesma que a função de cluster de administrador de cluster.


Por exemplo, você pode executar ações associadas a um conjunto de verbos (`create`) para operar em um conjunto de nomes de recursos (`templates`). Para exibir os detalhes dessas funções e seus conjuntos de verbos e recursos, execute o seguinte comando:

`$ oc describe clusterrole/osa-customer-admin`

Os nomes de verbo não necessariamente são mapeados diretamente para comandos `oc`. Eles correspondem mais geralmente aos tipos de operações da CLI que você pode executar. 

Por exemplo, ter o verbo `list` significa que você pode exibir uma lista de todos os objetos de um nome de recurso (`oc get`). O verbo `get` significa que você pode exibir os detalhes de um objeto específico se souber seu nome (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Configurar a função de administrador do cliente

Você pode configurar a função Administrador do cliente somente durante a criação do cluster, fornecendo o sinalizador `--customer-admin-group-id`. Para saber como configurar Azure Active Directory e o grupo de administradores, confira [integração Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Passos seguintes

Configure a função OSA-Customer-admin:
> [!div class="nextstepaction"]
> [Integração do Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md)
