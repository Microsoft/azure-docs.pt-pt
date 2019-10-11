---
title: Tutorial-configurar funções RBAC (controle de acesso baseado em função) no AKS (serviço de kubernetes do Azure) usando o Ansible
description: Saiba como usar o Ansible para configurar o RBAC no cluster do AKS (serviço kubernetes do Azure)
keywords: Ansible, Azure, DevOps, Bash, cloudshell, manual, AKs, contêiner, AKs, kubernetes, Azure Active Directory, RBAC
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 36a6f5ade7a60a989d2e80f2405aaa2d1d50b756
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242341"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: configurar funções RBAC (controle de acesso baseado em função) no AKS (serviço de kubernetes do Azure) usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS pode ser configurado para usar o [Azure Active Directory (AD)](/azure/active-directory/) para autenticação de usuário. Uma vez configurado, você usa o token de autenticação do Azure AD para entrar no cluster AKS. O RBAC pode ser baseado na identidade de um usuário ou em uma associação de grupo de diretório.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster AKS habilitado para Azure AD
> * Configurar uma função RBAC no cluster

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Instalar a biblioteca do redhat OpenShift** -  @ no__t-2

## <a name="configure-azure-ad-for-aks-authentication"></a>Configurar o Azure AD para autenticação AKS

Ao configurar o Azure AD para autenticação AKS, dois aplicativos do Azure AD são configurados. Esta operação deve ser concluída por um administrador de locatário do Azure. Para obter mais informações, consulte [integrar Azure Active Directory com AKs](/azure/aks/aad-integration#create-the-server-application). 

No administrador de locatários do Azure, obtenha os seguintes valores:

- Segredo do aplicativo do servidor
- ID do aplicativo do servidor
- ID do aplicativo cliente 
- ID do inquilino

Esses valores são necessários para executar o guia estratégico de exemplo.  

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Nesta seção, você criará um AKS com o [aplicativo Azure ad](#configure-azure-ad-for-aks-authentication).

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- O guia estratégico carrega `ssh_key` de `~/.ssh/id_rsa.pub`. Se você modificá-lo, use o formato de linha única-começando com "ssh-RSA" (sem as aspas).
- Os valores `client_id` e `client_secret` são carregados de `~/.azure/credentials`, que é o arquivo de credencial padrão. Você pode definir esses valores para sua entidade de serviço ou carregar esses valores de variáveis de ambiente:

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

## <a name="get-the-azure-ad-object-id"></a>Obter a ID de objeto do Azure AD

Para criar uma associação RBAC, primeiro você precisa obter a ID de objeto do Azure AD. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. No campo de pesquisa na parte superior da página, digite `Azure Active Directory`. 

1. Clique em `Enter`.

1. No menu **gerenciar** , selecione **usuários**.

1. No campo nome, pesquise sua conta.

1. Na coluna **nome** , selecione o link para sua conta.

1. Na seção **identidade** , copie a **ID de objeto**.

    ![Copie a ID de objeto do Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Criar Associação RBAC

Nesta seção, você cria uma associação de função ou de função de cluster em AKS. 

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

Substitua o espaço reservado `&lt;your-aad-account>` pela sua ID de [objeto](#get-the-azure-ad-object-id)de locatário do Azure AD.

Salve o guia estratégico a seguir-que implanta sua nova função no AKS-as `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Executar o guia estratégico de exemplo

Esta seção lista o manual de exemplo completo que chama as tarefas que criam neste artigo. 

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

Na seção `vars`, substitua os seguintes espaços reservados pelas informações do Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Execute o manual completo usando o comando `ansible-playbook`:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Verificar os resultados

Nesta seção, você usará kubectl listar os nós que criam neste artigo.

Digite o seguinte comando em um prompt de terminal:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

O comando irá direcioná-lo a uma página de autenticação. Entre com sua conta do Azure.

Uma vez autenticado, o kubectl lista os nós de maneira semelhante aos seguintes resultados:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados neste artigo. 

Salve o código a seguir como `cleanup.yml`:

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ansible no Azure](/azure/ansible/)
