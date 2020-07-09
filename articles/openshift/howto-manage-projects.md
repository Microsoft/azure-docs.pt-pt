---
title: Gerir recursos em Azure Red Hat OpenShift Microsoft Docs
description: Gerir projetos, modelos, streams de imagem em um cluster Azure Red Hat OpenShift
services: openshift
keywords: projetos de abertura chapéu vermelho solicita auto-provisioner
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79139118"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Gerir projetos, modelos, streams de imagem em um cluster Azure Red Hat OpenShift 

Numa Plataforma de Contentores OpenShift, os projetos são usados para agrupar e isolar objetos relacionados. Como administrador, pode dar aos desenvolvedores acesso a projetos específicos, permitir-lhes criar os seus próprios projetos e conceder-lhes direitos administrativos a projetos individuais.

## <a name="self-provisioning-projects"></a>Projetos de auto-provisão

Pode permitir que os desenvolvedores criem os seus próprios projetos. Um ponto final da API é responsável por provisão de um projeto de acordo com um modelo nomeado pedido de projeto. A consola web e o `oc new-project` comando usam este ponto final quando um desenvolvedor cria um novo projeto.

Quando um pedido de projeto é apresentado, a API substitui os seguintes parâmetros no modelo:

| Parâmetro               | Descrição                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | O nome do projeto. Necessário.             |
| PROJECT_DISPLAYNAME     | O nome de exibição do projeto. Pode estar vazio. |
| PROJECT_DESCRIPTION     | A descrição do projeto. Pode estar vazio.  |
| PROJECT_ADMIN_USER      | O nome de utilizador do utilizador que administra.       |
| PROJECT_REQUESTING_USER | O nome de utilizador do utilizador que solicita.           |

O acesso à API é concedido a desenvolvedores com a função de cluster de auto-provisioners vinculativa. Esta funcionalidade encontra-se disponível para todos os programadores autenticados por padrão.

## <a name="modify-the-template-for-a-new-project"></a>Modifique o modelo para um novo projeto 

1. Faça login como um utilizador com `customer-admin` privilégios.

2. Editar o modelo de pedido de projeto padrão.

   ```
   oc edit template project-request -n openshift
   ```

3. Remova o modelo de projeto padrão do processo de atualização Azure Red Hat OpenShift (ARO), adicionando a seguinte anotação:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   O modelo de pedido de projeto não será atualizado pelo processo de atualização da ARO. Isto permite aos clientes personalizar o modelo e preservar estas personalizações quando o cluster é atualizado.

## <a name="disable-the-self-provisioning-role"></a>Desativar o papel de auto-provisão

Pode impedir que um grupo de utilizadores autenticado se auto-provisa novos projetos.

1. Faça login como um utilizador com `customer-admin` privilégios.

2. Editar a ligação do papel de cluster dos auto-provisionadores.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Retire a função do processo de atualização da ARO adicionando a seguinte anotação: `openshift.io/reconcile-protect: "true"` .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Alterar a função de cluster vinculativa para evitar `system:authenticated:oauth` a criação de projetos:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
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

## <a name="manage-default-templates-and-imagestreams"></a>Gerir modelos e imagens

No Azure Red Hat OpenShift, pode desativar atualizações para quaisquer modelos e fluxos de imagem padrão dentro `openshift` do espaço de nome.
Para desativar as atualizações para ALL `Templates` e no espaço de `ImageStreams` `openshift` nome:

1. Faça login como um utilizador com `customer-admin` privilégios.

2. Editar `openshift` espaço de nome:

   ```
   oc edit namespace openshift
   ```

3. Remova `openshift` o espaço de nome do processo de atualização ARO adicionando a seguinte anotação:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Qualquer objeto individual no `openshift` espaço de nomes pode ser removido do processo de atualização `openshift.io/reconcile-protect: "true"` adicionando-lhe anotação.

## <a name="next-steps"></a>Próximos passos

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
