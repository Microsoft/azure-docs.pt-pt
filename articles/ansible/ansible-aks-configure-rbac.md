---
title: Tutorial - configurar funções (RBAC) do controle de acesso baseado em funções no Azure Kubernetes Service (AKS) com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para configurar o RBAC no cluster do Azure Kubernetes Service(AKS)
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, contentores, aks, kubernetes, o azure active directory, rbac
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: bdf3d719627673bfb2c6b25957abb849c46d4e9e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767265"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Configurar funções (RBAC) do controle de acesso baseado em funções no Azure Kubernetes Service (AKS) com o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS pode ser configurado para utilizar [do Azure Active Directory (AD)](/azure/active-directory/) para autenticação de utilizador. Uma vez configurado, utilize o token de autenticação do Azure AD para iniciar sessão no cluster do AKS. O RBAC pode basear-se na identidade de um utilizador ou associação de grupo do diretório.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster do AKS habilitados no AD do Azure
> * Configurar uma função RBAC do cluster

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Instalar a biblioteca do Red Hat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Configurar o Azure AD para autenticação do AKS

Ao configurar o Azure AD para autenticação do AKS, são configuradas duas aplicações do Azure AD. Esta operação deve ser concluída por um administrador de inquilino do Azure. Para obter mais informações, consulte [integrar o Azure Active Directory com o AKS](/azure/aks/aad-integration#create-server-application). 

Desde o administrador de inquilino do Azure, obtém os seguintes valores:

- Segredo de aplicação de servidor
- ID da aplicação de servidor
- ID de aplicação de cliente 
- ID do inquilino

Estes valores são necessários para executar o playbook de exemplo.  

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Nesta secção, vai criar um AKS com o [aplicação do Azure AD](#configure-azure-ad-for-aks-authentication).

Seguem-se algumas notas essenciais a considerar ao trabalhar com o playbook de exemplo:

- Carrega o playbook `ssh_key` partir `~/.ssh/id_rsa.pub`. Se modificá-lo, utilize o formato de linha única - começando por "ssh-rsa" (sem as aspas).
- O `client_id` e `client_secret` valores são carregados a partir do `~/.azure/credentials`, que é o arquivo de credencial padrão. Pode definir esses valores ao seu serviço principal ou carregar estes valores de variáveis de ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Guarde o manual de procedimentos seguinte como `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Obter o ID de objeto do Azure AD

Para criar um enlace de RBAC, tem primeiro de obter o ID de objeto do Azure AD. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. No campo de pesquisa na parte superior da página, introduza `Azure Active Directory`. 

1. Clique em `Enter`.

1. Na **Manage** menu, selecione **utilizadores**.

1. No campo de nome, procure a sua conta.

1. Na **nome** coluna, selecione a ligação à sua conta.

1. Na **identidade** secção, copie a **ID de objeto**.

    ![Copie o ID de objeto do Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Criar o enlace de RBAC

Nesta secção, vai criar um enlace de função ou enlace de função de cluster no AKS. 

Guarde o manual de procedimentos seguinte como `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Substitua a `&lt;your-aad-account>` marcador de posição no seu inquilino do Azure AD [ID de objeto](#get-the-azure-ad-object-id).

Guardar o playbook seguinte - que implementa a sua nova função de AKS – como `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Executar o playbook de exemplo

Esta secção lista o playbook de exemplo completo que chama as tarefas que criar neste artigo. 

Guarde o manual de procedimentos seguinte como `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

Na `vars` secção, substitua os marcadores de posição seguintes com as suas informações do Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Executar o playbook completado com o `ansible-playbook` comando:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Verificar os resultados

Nesta secção, vai utilizar kubectl lista os nós criando neste artigo.

Introduza o seguinte comando numa linha de comandos de terminal:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

O comando irá direcioná-lo para uma página de autenticação. Inicie sessão com a sua conta do Azure.

Uma vez autenticado, o kubectl lista os nós de forma semelhante para os seguintes resultados:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos criados neste artigo. 

Guarde o código a seguir como `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ansible no Azure](/azure/ansible/)