---
title: Gerir recursos no Azure Red Hat OpenShift [ Microsoft Docs
description: Gerir projetos, modelos, fluxos de imagem num cluster OpenShift do Chapéu Vermelho Azure
services: openshift
keywords: chapéu vermelho projetos abertos pede auto-provisioner
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79139118"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Gerir projetos, modelos, fluxos de imagem num cluster OpenShift do Chapéu Vermelho Azure 

Numa Plataforma de Contentores OpenShift, os projetos são usados para agrupar e isolar objetos relacionados. Como administrador, pode dar aos desenvolvedores acesso a projetos específicos, permitir-lhes criar os seus próprios projetos e conceder-lhes direitos administrativos a projetos individuais.

## <a name="self-provisioning-projects"></a>Projetos de autoprovisionamento

Pode permitir que os desenvolvedores criem os seus próprios projetos. Um ponto final da API é responsável por fornecer um projeto de acordo com um modelo chamado pedido de projeto. A consola web `oc new-project` e o comando usam este ponto final quando um desenvolvedor cria um novo projeto.

Quando um pedido de projeto é submetido, a API substitui os seguintes parâmetros no modelo:

| Parâmetro               | Descrição                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | O nome do projeto. Necessário.             |
| PROJECT_DISPLAYNAME     | O nome de exibição do projeto. Pode estar vazio. |
| PROJECT_DESCRIPTION     | A descrição do projeto. Pode estar vazio.  |
| PROJECT_ADMIN_USER      | O nome de utilizador do utilizador administrador.       |
| PROJECT_REQUESTING_USER | O nome de utilizador do utilizador que solicita.           |

O acesso à API é concedido a programadores com o papel de cluster auto-provisionador vinculativo. Esta funcionalidade está disponível para todos os desenvolvedores autenticados por padrão.

## <a name="modify-the-template-for-a-new-project"></a>Modificar o modelo para um novo projeto 

1. Faça login como `customer-admin` utilizador com privilégios.

2. Editar o modelo de pedido de projeto padrão.

   ```
   oc edit template project-request -n openshift
   ```

3. Remova o modelo de projeto predefinido do processo de atualização Do Hat OpenShift (ARO) do Azure Red Hat, adicionando a seguinte anotação:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   O modelo de pedido de projeto não será atualizado pelo processo de atualização ARO. Isto permite que os clientes personalizem o modelo e preservem estas personalizações quando o cluster é atualizado.

## <a name="disable-the-self-provisioning-role"></a>Desativar o papel de auto-provisionamento

Pode evitar que um grupo de utilizadores autenticado se auto-aprovisione novos projetos.

1. Faça login como `customer-admin` utilizador com privilégios.

2. Editar o papel de cluster auto-provisionadores vinculativo.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Retire a função do processo de atualização ARO adicionando a seguinte anotação: `openshift.io/reconcile-protect: "true"`.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Alterar a função `system:authenticated:oauth` de cluster vinculada para evitar a criação de projetos:

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

## <a name="manage-default-templates-and-imagestreams"></a>Gerir modelos padrão e imagensStreams

No Azure Red Hat OpenShift, pode desativar atualizações para `openshift` quaisquer modelos padrão e fluxos de imagem dentro do espaço de nome.
Para desativar `Templates` as `ImageStreams` `openshift` atualizações para TODOS e no espaço de nome:

1. Faça login como `customer-admin` utilizador com privilégios.

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

   Qualquer objeto individual `openshift` no espaço de nome pode ser removido `openshift.io/reconcile-protect: "true"` do processo de atualização adicionando-lhe anotação.

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
