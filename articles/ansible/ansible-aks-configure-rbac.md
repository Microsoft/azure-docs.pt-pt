---
title: Tutorial - Configure funções de controlo de acesso baseado em funções (RBAC) no Serviço Azure Kubernetes (AKS) utilizando ansible
description: Saiba como usar o Ansible para configurar o RBAC no cluster Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes, azure ative diretório, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 5fac42383ee56318cc4b8f39323c02d05853dbb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76836971"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Configure funções de controlo de acesso baseado em funções (RBAC) no Serviço Azure Kubernetes (AKS) utilizando ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

O AKS pode ser configurado para utilizar o [Diretório Ativo Azure (AD)](/azure/active-directory/) para autenticação do utilizador. Uma vez configurado, utilize o seu símbolo de autenticação Azure AD para assinar no cluster AKS. O RBAC pode basear-se na identidade de um utilizador ou na adesão ao grupo de diretórios.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster AKS ativado por AD Azure
> * Configure uma função RBAC no cluster

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Instale a biblioteca RedHat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Configure Azure AD para autenticação AKS

Ao configurar o Azure AD para autenticação AKS, duas aplicações Azure AD estão configuradas. Esta operação deve ser concluída por um administrador de inquilinos azure. Para mais informações, consulte Integrar o [Diretório Ativo Azure com AKS.](/azure/aks/aad-integration#create-the-server-application) 

Do administrador de inquilinos Azure, obtenha os seguintes valores:

- Segredo de aplicativo de servidor
- ID do aplicativo do servidor
- ID da aplicação do cliente 
- ID do inquilino

Estes valores são necessários para executar o livro de jogadas da amostra.  

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Nesta secção, cria-se uma AKS com a [aplicação Azure AD](#configure-azure-ad-for-aks-authentication).

Aqui estão algumas notas-chave a considerar ao trabalhar com o livro de jogadas da amostra:

- O livro `ssh_key` `~/.ssh/id_rsa.pub`de jogadas carrega de. Se o modificar, utilize o formato de linha única - começando com "ssh-rsa" (sem as cotações).
- Os `client_id` `client_secret` valores e `~/.azure/credentials`valores são carregados de , que é o ficheiro credencial padrão. Pode definir estes valores para o seu principal de serviço ou carregar estes valores a partir de variáveis ambientais:

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
  azure_rm_aksversion_facts:
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

## <a name="get-the-azure-ad-object-id"></a>Obtenha o ID de Objeto AD Azure

Para criar uma ligação RBAC, primeiro precisa obter o ID de objeto AD Azure. 

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. No campo de pesquisa no topo `Azure Active Directory`da página, introduza . 

1. Clique em `Enter`.

1. No menu **Gerir,** selecione **Utilizadores**.

1. No campo de nome, procure a sua conta.

1. Na coluna **Nome,** selecione o link para a sua conta.

1. Na secção **Identidade,** copie o ID do **Objeto**.

    ![Copie o ID do Objeto Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Criar ligação RBAC

Nesta secção, cria-se uma função vinculativa ou de cluster vinculativa no AKS. 

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

Substitua `<your-aad-account>` o espaço reservado pelo seu Id de Objeto de [Objeto](#get-the-azure-ad-object-id)de Inquilino AD Azure .

Guarde o seguinte livro - que implementa `aks-kube-deploy.yml`o seu novo papel na AKS - como:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Executar o livro de jogadas da amostra

Esta secção lista o manual completo da amostra que chama as tarefas que criam neste artigo. 

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

Na `vars` secção, substitua os seguintes espaços reservados pela informação da AD Azure:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Executar o manual `ansible-playbook` completo usando o comando:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Verifique os resultados

Nesta secção, você usa a lista kubectl os nós criados neste artigo.

Introduza o seguinte comando num aviso terminal:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

O comando irá direcioná-lo para uma página de autenticação. Inscreva-se na sua conta Azure.

Uma vez autenticado, kubectl lista os nós de forma semelhante aos seguintes resultados:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague os recursos criados neste artigo. 

Guarde o `cleanup.yml`seguinte código como:

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ansible no Azure](/azure/ansible/)
