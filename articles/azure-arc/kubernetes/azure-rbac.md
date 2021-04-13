---
title: Azure RBAC para Azure Arc permitiu clusters Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Utilize o Azure RBAC para verificação de autorização em agrupamentos de Kubernetes habilitados
ms.openlocfilehash: 0ee5f86ce12a39d86754d2e6e88263d8a03a012b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304210"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Integre o Azure Ative Directory com clusters Azure Arc habilitados kubernetes

Os tipos de objetos De [ClusterRoleBinding e RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) ajudam a definir a autorização em Kubernetes de forma nativa. Utilizando esta funcionalidade, pode utilizar o Azure Ative Directory e atribuições de funções em Azure para controlar as verificações de autorização no cluster. Isto implica que agora pode usar atribuições de funções Azure para controlar granulivelmente quem pode ler, escrever, eliminar os seus objetos Kubernetes, tais como Implementação, Pod e Serviço

Uma visão geral conceptual desta funcionalidade está disponível no [Azure RBAC - Azure Arc ativou o artigo de Kubernetes.](conceptual-azure-rbac.md)

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Instalar ou atualizar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para a versão >= 2.16.0

- Instale a `connectedk8s` extensão Azure CLI da versão >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Se a `connectedk8s` extensão já estiver instalada, pode atualizá-la para a versão mais recente utilizando o seguinte comando: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Um arco Azure existente permitiu o agrupamento de kubernetes conectado.
    - Se ainda não ligou um cluster, use o nosso [quickstart](quickstart-connect-cluster.md).
    - [Atualize os seus agentes](agent-upgrade.md#manually-upgrade-agents) para a versão >= 1.1.0.

> [!NOTE]
> Esta funcionalidade não pode ser configurada para ofertas geridas de Kubernetes de fornecedores de nuvem como o Elastic Kubernetes Service ou o Google Kubernetes Engine, onde o utilizador não tem acesso `apiserver` ao cluster. Para os clusters Azure Kubernetes Service (AKS), esta [funcionalidade está disponível de forma nativa](../../aks/manage-azure-rbac.md) e não requer que o cluster AKS esteja ligado ao Arco de Azure.

## <a name="set-up-azure-ad-applications"></a>Configurar aplicações AD Azure

### <a name="create-server-application"></a>Criar aplicação de servidor

1. Crie uma nova aplicação AD AZure e obtenha o seu `appId` valor, que é usado em etapas posteriores como `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Atualizar as reclamações de adesão do grupo de candidatura:

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Crie um principal de serviço e obtenha o seu `password` valor de campo, que é necessário mais tarde como `serverApplicationSecret` quando ativar esta funcionalidade no cluster:

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Conceda permissões de API ao pedido:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * Este passo tem de ser executado por um administrador inquilino da Azure.
    > * Para a utilização desta funcionalidade em produção, recomenda-se criar uma aplicação de servidor diferente para cada cluster.

### <a name="create-client-application"></a>Criar aplicação de cliente

1. Crie uma nova aplicação AD AZure e obtenha o seu valor 'appId', que é usado em etapas posteriores como `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Criar um principal de serviço para esta aplicação de cliente:

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. Obtenha a `oAuthPermissionId` aplicação para o servidor:

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. Conceda as permissões necessárias para o pedido do cliente:

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Criar uma atribuição de função para a aplicação do servidor

A aplicação do servidor necessita das `Microsoft.Authorization/*/read` permissões para verificar se o utilizador que estoende o pedido é autorizado nos objetos Kubernetes que fazem parte do pedido.

1. Crie um ficheiro nomeado accessCheck.jscom os seguintes conteúdos:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Substitua-a `<subscription-id>` pelo ID de subscrição real.

2. Execute o seguinte comando para criar o novo papel personalizado:

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. A partir da saída do comando acima, guarde o valor do `id` campo, que é utilizado em etapas posteriores como `roleId` .

4. Criar uma atribuição de funções na aplicação do servidor como destinatário utilizando a função acima criada:

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Ativar o Azure RBAC no cluster

1. Ativar o Azure RBAC no seu arco ativado pelo cluster Kubernetes executando o seguinte comando:

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. Antes de executar o comando acima, certifique-se de que o `kubeconfig` ficheiro da máquina está a apontar para o cluster sobre o qual ativa a função Azure RBAC.
    > 2. Utilize `--skip-azure-rbac-list` com o comando acima para uma lista separada por vírgula de nomes de utilizador/e-mail/oid submetidos a verificações de autorização utilizando objetos nativos de Kubernetes ClusterRoleBinding e RoleBinding em vez de Azure RBAC.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>Para um cluster genérico onde não exista um reconciliador em execução na especificação do apiserver:

1. SSH em cada nó mestre do cluster e execute os seguintes passos:

    1. Abrir `apiserver` manifesto no modo de edição:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Adicione a seguinte especificação `volumes` em:
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Adicione a seguinte especificação `volumeMounts` em:

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Adicione os `apiserver` seguintes argumentos:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Se o cluster Kubernetes for de versão >= 1.19.0, então também é necessário definir o `apiserver argument` seguinte:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Guarde e saia do editor para atualizar a `apiserver` cápsula.


### <a name="for-a-cluster-created-using-cluster-api"></a>Para um cluster criado usando Cluster API

1. Copie o segredo do guarda contendo a autenticação e autorização webhook config `from the workload cluster` arquivos para a sua máquina:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Altere o `namespace` campo no ficheiro para o espaço de `azure-arc-guard-manifests.yaml` nomes dentro do cluster de gestão onde está a aplicar os recursos personalizados para a criação de clusters de carga de trabalho.

1. Aplique este manifesto:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Editar o `KubeadmControlPlane` objeto `kubectl edit kcp <clustername>-control-plane` executando:
    
    1. Adicione o seguinte corte em `files:` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Adicione o seguinte corte em `apiServer:`  ->  `extraVolumes:` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Adicione o seguinte corte em `apiServer:`  ->  `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Guarde e saia para atualizar o `KubeadmControlPlane` objeto. Aguarde que estas alterações sejam realizadas no cluster de carga de trabalho.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Criar atribuições de funções para os utilizadores acederem ao cluster

Os proprietários do Azure Arc habilitados o recurso Kubernetes podem usar funções incorporadas ou funções personalizadas para garantir a outros utilizadores acesso ao cluster Kubernetes.

### <a name="built-in-roles"></a>Funções incorporadas

| Função | Description |
|---|---|
| [Espectador Azure Arc Kubernetes](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | Permite o acesso apenas à leitura para ver a maioria dos objetos num espaço de nome. Este papel não permite ver segredos. Isto porque `read` a permissão em segredos permitiria o acesso a `ServiceAccount` credenciais no espaço de nomes, o que por sua vez permitiria o acesso da API usando isso `ServiceAccount` (uma forma de escalada de privilégio). |
| [Escritor Azure Arc Kubernetes](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | Permite ler/escrever o acesso à maioria dos objetos num espaço de nome. Esta função não permite visualizar ou modificar papéis ou encadernações de papéis. No entanto, esta função permite aceder a segredos e a correr como qualquer outro `ServiceAccount` no espaço de nomes, para que possa ser usado para obter os níveis de acesso a API de qualquer um `ServiceAccount` no espaço de nome. |
| [Administrador Azure Arc Kubernetes](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | Permite o acesso administrativo. Destina-se a ser concedido dentro de um espaço de nome usando um RoleBinding. Se usado num RoleBinding, permite ler/escrever acesso à maioria dos recursos num espaço de nome, incluindo a capacidade de criar papéis e encadernações de papéis dentro do espaço de nomes. Esta função não permite escrever acesso à quota de recursos ou ao próprio espaço de nome. |
| [Azure Arc Kubernetes Cluster Admin](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | Permite o acesso do super-utilizador para executar qualquer ação em qualquer recurso. Quando usado num ClusterRoleBinding, dá total controlo sobre todos os recursos do cluster e em todos os espaços de nome. Quando usado num RoleBinding, dá total controlo sobre todos os recursos no espaço de nome da ligação do papel, incluindo o próprio espaço de nome.|

Pode criar atribuições de funções no cluster de Kubernetes ativados no cluster da `Access Control (IAM)` lâmina do recurso cluster no portal Azure. Também pode utilizar comandos Azure CLI, como mostrado abaixo:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

onde `AZURE-AD-ENTITY-ID` poderia ser um nome de utilizador (por exemplo, ) ou mesmo o de um principal de testuser@mytenant.onmicrosoft.com `appId` serviço.

Aqui está outro exemplo de criação de uma atribuição de papel a um espaço de nome específico dentro do cluster -

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Embora as atribuições de funções telescópios para o cluster possam ser criadas usando o portal Azure ou CLI, as atribuições de funções telescópios para espaços de nome só podem ser criadas usando o CLI.

### <a name="custom-roles"></a>Funções personalizadas

Pode optar por criar a sua própria definição de papel para uso em atribuições de funções.

Caminhe pelo exemplo abaixo de uma definição de função que permite que um utilizador leia apenas implementações. Para mais informações, consulte [a lista completa de ações de dados que pode utilizar para construir uma definição de papel.](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)

Copie o objeto JSON abaixo num ficheiro chamado custom-role.jsligado. Substitua o `<subscription-id>` espaço reservado pelo ID de subscrição real. A função personalizada abaixo utiliza uma das ações de dados e permite-lhe visualizar todas as implementações no âmbito (cluster/namespace) onde a atribuição de funções é criada.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Crie a definição de função executando o comando abaixo a partir da pasta onde `custom-role.json` guardou:

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Crie uma atribuição de funções utilizando esta definição de função personalizada:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Configure kubectl com credenciais de utilizador

Existem duas formas de obter `kubeconfig` ficheiros necessários para aceder ao cluster:
1. Utilize a função [Cluster Connect](cluster-connect.md) `az connectedk8s proxy` () do aglomerado de Kubernetes ativado pelo Arco Azure.
1. Cluster admin partilha `kubeconfig` ficheiro com todos os outros utilizadores.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Se estiver a aceder ao cluster utilizando a função Cluster Connect

Execute o seguinte comando para iniciar o processo de procuração:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Depois de o processo de procuração estar em funcionamento, pode abrir outro separador na sua consola para começar a [enviar os seus pedidos para o agrupamento.](#sending-requests-to-cluster)

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Se o administrador do cluster partilhou o `kubeconfig` ficheiro consigo 

1. Execute o seguinte comando para definir credenciais para o utilizador:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Abra o `kubeconfig` ficheiro que criou anteriormente. Em `contexts` , verifique o contexto associado aos pontos de cluster das credenciais de utilizador criadas no passo anterior.

1. Adicione a definição **de modo config** sob o config do utilizador:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>Envio de pedidos para cluster

1. Executar qualquer `kubectl` comando. Por exemplo:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. Uma vez solicitado para uma autenticação baseada no navegador, copie o URL de login do dispositivo `https://microsoft.com/devicelogin` e abra no seu navegador web.

1. Introduza o código impresso na sua consola, copie e cole o código do seu terminal na entrada de autenticação do dispositivo.

1. Introduza o nome de utilizador testuser@mytenant.onmicrosoft.com () e senha associada.

1. Se vir uma mensagem de erro como esta, significa que não está autorizado a aceder ao recurso solicitado:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Um administrador precisa de criar uma nova atribuição de funções autorizando este utilizador a ter acesso ao recurso.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> Ligue-se de forma segura ao cluster utilizando [o Cluster Connect](cluster-connect.md)