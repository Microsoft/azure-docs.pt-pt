---
title: Gerenciar recursos no Azure Red Hat OpenShift | Microsoft Docs
description: Gerenciar projetos, modelos, fluxos de imagens em um cluster do Azure Red Hat OpenShift
services: openshift
keywords: os projetos do Red Hat openshift solicitam o autoprovisionador
author: mjudeikis
ms.author: b-majude
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 5028ce3c71538e67b50a15abb6076871d5af7050
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559567"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Gerenciar projetos, modelos, fluxos de imagens em um cluster do Azure Red Hat OpenShift 

Em uma plataforma de contêiner OpenShift, os projetos são usados para agrupar e isolar objetos relacionados. Como administrador, você pode dar aos desenvolvedores acesso a projetos específicos, permitir que eles criem seus próprios projetos e conceder a eles direitos administrativos a projetos individuais.

## <a name="self-provisioning-projects"></a>Projetos de autoprovisionamento

Você pode permitir que os desenvolvedores criem seus próprios projetos. Um ponto de extremidade de API é responsável por provisionar um projeto de acordo com um modelo chamado Project-Request. O console Web e o `oc new-project` comando usam esse ponto de extremidade quando um desenvolvedor cria um novo projeto.

Quando uma solicitação de projeto é enviada, a API substitui os seguintes parâmetros no modelo:

| Parâmetro               | Descrição                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | O nome do projeto. Necessário.             |
| PROJECT_DISPLAYNAME     | O nome de exibição do projeto. Pode estar vazio. |
| PROJECT_DESCRIPTION     | A descrição do projeto. Pode estar vazio.  |
| PROJECT_ADMIN_USER      | O nome do usuário administrador.       |
| PROJECT_REQUESTING_USER | O nome do usuário do solicitante.           |

O acesso à API é concedido aos desenvolvedores com a associação de função de cluster de autoprovisionadores. Esse recurso está disponível para todos os desenvolvedores autenticados por padrão.

## <a name="modify-the-template-for-a-new-project"></a>Modificar o modelo para um novo projeto 

1. Faça logon como um usuário com `customer-admin` privilégios.

2. Edite o modelo de solicitação de projeto padrão.

   ```
   oc edit template project-request -n openshift
   ```

3. Remova o modelo de projeto padrão do processo de atualização da toa (Red Hat OpenShift) do Azure adicionando a seguinte anotação:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   O modelo de solicitação de projeto não será atualizado pelo processo de atualização da toa. Isso permite que os clientes personalizem o modelo e preserve essas personalizações quando o cluster é atualizado.

## <a name="disable-the-self-provisioning-role"></a>Desabilitar a função de autoprovisionamento

Você pode impedir que um grupo de usuários autenticado provisione novos projetos automaticamente.

1. Faça logon como um usuário com `customer-admin` privilégios.

2. Edite a associação de função de cluster de autoprovisionadores.

   ```
   oc edit clusterrolebinding self-provisioners
   ```

3. Remova a função do processo de atualização da toa adicionando a seguinte anotação: `openshift.io/reconcile-protect: "true"`.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Altere a associação de função de cluster `system:authenticated:oauth` para evitar a criação de projetos:

   ```
   apiVersion: authorization.openshift.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Gerenciar modelos padrão e imageStreams

No Azure Red Hat OpenShift, você pode desabilitar atualizações para todos os modelos padrão e fluxos de `openshift` imagem dentro do namespace.
Para desabilitar atualizações para todos `Templates` os `ImageStreams` e `openshift` no namespace:

1. Faça logon como um usuário com `customer-admin` privilégios.

2. Editar `openshift` namespace:

   ```
   oc edit namespace openshift
   ```

3. Remova `openshift` o namespace do processo de atualização da toa adicionando a seguinte anotação:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Qualquer objeto individual no `openshift` namespace pode ser removido do processo de atualização adicionando anotação `openshift.io/reconcile-protect: "true"` a ele.

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
