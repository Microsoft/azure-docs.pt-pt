---
title: Função de administrador de cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Uso e atribuição de funções de administrador de cluster do Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709956"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Função de administrador de clientes do Red Hat OpenShift do Azure

Como um administrador de cluster toa (Azure Red Hat OpenShift) de um cluster OpenShift, sua conta tem mais permissões e acesso a todos os projetos criados pelo usuário.

Quando sua conta tiver a função de autorização OSA-Customer-admins associada a ela, ela poderá gerenciar automaticamente um projeto.

> [!Note] 
> OSA-Customer-admin clusterrole não é o mesmo que o cluster-admin clusterrole


Por exemplo, você pode executar ações associadas a um conjunto de verbos (`create`) para operar em um conjunto de nomes de recursos (`templates`). Para exibir os detalhes dessas funções e seus conjuntos de verbos e recursos, execute o seguinte comando:

`$ oc describe clusterrole/osa-customer-admin`

Os nomes de verbo não necessariamente são mapeados diretamente para comandos de OC, mas, em vez disso, são equivalentes aos tipos de operações de CLI que você pode executar. Por exemplo, ter o verbo `list` significa que você pode exibir uma lista de todos os objetos de um determinado nome de recurso (`oc get`), enquanto o verbo `get` significa que você pode exibir os detalhes de um objeto específico se souber seu nome (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>Como configurar a função de administrador de clientes

A função Administrador de clientes pode ser configurada somente durante a criação do cluster, fornecendo o sinalizador `--customer-admin-group-id`. Como configurar o grupo de Azure Active Directory e administradores siga como fazer o guia: [Integração do Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>Passos seguintes

Como configurar a função OSA-Customer-admin:
> [!div class="nextstepaction"]
> [Integração do Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md)
