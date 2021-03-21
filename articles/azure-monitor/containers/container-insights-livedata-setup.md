---
title: Configurar informações de recipientes Live Data (pré-visualização) | Microsoft Docs
description: Este artigo descreve como configurar a visão em tempo real dos troncos de contentores (stdout/stderr) e eventos sem utilizar kubectl com insights de contentores.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: references_regions
ms.openlocfilehash: 4302bdbb3d71c890f7fb0cfb82ab5f8d5aecbd43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713784"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Como configurar a funcionalidade Dados Ao Vivo (pré-visualização)

Para visualizar dados ao vivo (pré-visualização) com insights do Contentor dos clusters Azure Kubernetes Service (AKS), é necessário configurar a autenticação para conceder permissão de acesso aos seus dados Kubernetes. Esta configuração de segurança permite o acesso em tempo real aos seus dados através da API de Kubernetes diretamente no portal Azure.

Esta funcionalidade suporta os seguintes métodos para controlar o acesso aos registos, eventos e métricas:

- AKS sem autorização DECC RBAC de Kubernetes habilitada
- AKS habilitado com autorização do RBAC da Kubernetes
    - AKS configurado com o cluster de ligação de funções de **[clusterMonitoringUser](/rest/api/aks/managedclusters/listclustermonitoringusercredentials)**
- AKS ativado com Azure Ative Directory (AD) com base em saml

Estas instruções requerem tanto acesso administrativo ao seu cluster Kubernetes, como se configurar a utilização do Azure Ative Directory (AD) para autenticação do utilizador, acesso administrativo ao Azure AD.

Este artigo explica como configurar a autenticação para controlar o acesso à funcionalidade De Dados Ao Vivo (pré-visualização) do cluster:

- O controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC) permitiu o cluster AKS
- Azure Ative Directory integra o cluster AKS.


## <a name="authentication-model"></a>Modelo de autenticação

As funções De Dados Ao Vivo (pré-visualização) utilizam a API de Kubernetes, idêntica à `kubectl` ferramenta da linha de comando. Os pontos finais da API kubernetes utilizam um certificado auto-assinado, que o seu navegador não poderá validar. Esta funcionalidade utiliza um representante interno para validar o certificado com o serviço AKS, garantindo que o tráfego é confiável.

O portal Azure solicita-lhe que valide as suas credenciais de login para um cluster Azure Ative Directory e reencaminha-o para a configuração do registo do cliente durante a criação do cluster (e re-configurado neste artigo). Este comportamento é semelhante ao processo de autenticação exigido por `kubectl` .

>[!NOTE]
>A autorização para o seu cluster é gerida pela Kubernetes e o modelo de segurança com o qual está configurado. Os utilizadores que acedam a esta funcionalidade requerem permissão para descarregar a configuração kubernetes *(kubeconfig),* semelhante à `az aks get-credentials -n {your cluster name} -g {your resource group}` execução. Este ficheiro de configuração contém a autorização e o símbolo de autenticação para **a Azure Kubernetes Service Cluster User Role**, no caso dos clusters Azure RBAC e AKS sem autorização RBAC ativada por Kubernetes. Contém informações sobre a Azure AD e os dados de registo do cliente quando a AKS está ativada com o Azure Ative Directory (AD) com base em um único sinal baseado em SAML.

>[!IMPORTANT]
>Os utilizadores destas funcionalidades requerem [a Azure Kubernetes Cluster Cluster User Role](../../role-based-access-control/built-in-roles.md) para o cluster de forma a descarregar e utilizar esta `kubeconfig` funcionalidade. Os utilizadores **não** necessitam de acesso dos contribuintes ao cluster para utilizar esta funcionalidade.

## <a name="using-clustermonitoringuser-with-kubernetes-rbac-enabled-clusters"></a>Utilizando clusterMonitoringUser com clusters ativados por SIDA de Kubernetes

Para eliminar a necessidade de aplicar alterações de configuração adicionais para permitir que o **clusterUser** de ligação da função do utilizador Kubernetes aceda à funcionalidade De dados vivos (pré-visualização) depois de permitir a autorização [do AMC de Kubernetes,](#configure-kubernetes-rbac-authorization) a AKS adicionou uma nova ligação de cluster de kubernetes chamada **clusterMonitoringUser**. Esta ligação de funções de cluster tem todas as permissões necessárias fora da caixa para aceder à API de Kubernetes e aos pontos finais para utilizar a funcionalidade De Dados Ao Vivo (pré-visualização).

Para utilizar a funcionalidade Dados Ao Vivo (pré-visualização) com este novo utilizador, é necessário ser membro do [Grupo de Atendimento Azure Kubernetes](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) ou [do contributo](../../role-based-access-control/built-in-roles.md#contributor) no recurso cluster AKS. As informações do recipiente, quando ativadas, são configuradas para autenticar utilizando o clusterMonitoringUser por predefinição. Se a ligação de funções clusterMonitoringUser não existir num cluster, **o clusterUser** é utilizado para a autenticação. O colaborador dá-lhe acesso ao clusterMonitoringUser (se existir) e o Utilizador do Cluster de Serviços Azure Kuberenetes dá-lhe acesso ao clusterUser. Qualquer uma destas duas funções dá acesso suficiente à utilização desta funcionalidade.

A AKS divulgou esta nova função vinculativa em janeiro de 2020, pelo que os clusters criados antes de janeiro de 2020 não o têm. Se tiver um cluster que foi criado antes de janeiro de 2020, o novo **clusterMonitoringUser** pode ser adicionado a um cluster existente através da realização de uma operação PUT no cluster, ou realizando qualquer outra operação no cluster que execute uma operação PUT no cluster, como atualizar a versão do cluster.

## <a name="kubernetes-cluster-without-kubernetes-rbac-enabled"></a>Cluster Kubernetes sem Kubernetes RBAC ativado

Se tiver um cluster Kubernetes que não esteja configurado com autorização DE RBAC de Kubernetes ou integrado com um único sinal de Azure AD, não precisa seguir estes passos. Isto porque tem permissões administrativas por padrão numa configuração não-RBAC.

## <a name="configure-kubernetes-rbac-authorization"></a>Configure a autorização do RBAC da Kubernetes

Quando ativa a autorização do RBAC da Kubernetes, são utilizados dois utilizadores: **clusterUser** e **clusterAdmin** para aceder à API de Kubernetes. Isto é semelhante ao funcionamento `az aks get-credentials -n {cluster_name} -g {rg_name}` sem a opção administrativa. Isto significa que o **clusterUser** precisa de ter acesso aos pontos finais na API de Kubernetes.

Os passos de exemplo a seguir demonstram como configurar a ligação do papel do cluster a partir deste modelo de configuração yaml.

1. Copie e cole o ficheiro yaml e guarde-o como LogReaderRBAC.yaml.

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
       name: containerHealth-log-reader
    rules:
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"]
          resources:
             - "pods/log"
             - "events"
             - "nodes"
             - "pods"
             - "deployments"
             - "replicasets"
          verbs: ["get", "list"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
       name: containerHealth-read-logs-global
    roleRef:
       kind: ClusterRole
       name: containerHealth-log-reader
       apiGroup: rbac.authorization.k8s.io
    subjects:
    - kind: User
      name: clusterUser
      apiGroup: rbac.authorization.k8s.io
    ```

2. Para atualizar a sua configuração, executar o seguinte comando: `kubectl apply -f LogReaderRBAC.yaml` .

>[!NOTE]
> Se tiver aplicado uma versão anterior do `LogReaderRBAC.yaml` ficheiro ao seu cluster, atualize-o copiando e colando o novo código mostrado no passo 1 acima e, em seguida, executar o comando mostrado no passo 2 para aplicá-lo ao seu cluster.

## <a name="configure-ad-integrated-authentication"></a>Configurar a autenticação integrada em AD

Um cluster AKS configurado para utilizar o Azure Ative Directory (AD) para a autenticação do utilizador utiliza as credenciais de login da pessoa que acede a esta funcionalidade. Nesta configuração, pode iniciar sedutação num cluster AKS utilizando o seu token de autenticação AD Azure.

O registo do cliente Azure AD deve ser reformulado para permitir que o portal Azure redirecione as páginas de autorização como UM URL de redirecionamento fidedigno. Os utilizadores da Azure AD têm acesso direto aos mesmos pontos finais da API de Kubernetes através **do ClusterRoles** e **clusterRoleBindings.**

Para obter mais informações sobre a configuração avançada de segurança em Kubernetes, reveja a [documentação de Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

>[!NOTE]
>Se estiver a criar um novo cluster ativado por Kubernetes, consulte [o Integrate Azure Ative Directory com o Serviço Azure Kubernetes](../../aks/azure-ad-integration-cli.md) e siga os passos para configurar a autenticação Azure AD. Durante os passos para criar a aplicação do cliente, uma nota nessa secção realça os dois URLs de redirecionamento que precisa de criar para insights de contentores que correspondam aos especificados no Passo 3 abaixo.

### <a name="client-registration-reconfiguration"></a>Reconfiguração do registo do cliente

1. Localize o registo do cliente para o seu cluster Kubernetes em Azure AD sob **a Azure Ative Directory > Registos de App** no portal Azure.

2. Selecione **autenticação** a partir do painel esquerdo.

3. Adicione dois URLs de redirecionamento a esta lista como tipos de aplicação **Web.** O primeiro valor url base deve ser `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o valor URL de segunda base deve ser `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

    >[!NOTE]
    >Se estiver a utilizar esta funcionalidade no Azure China, o primeiro valor url base deve ser `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o valor de URL de segunda base deve ser `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

4. Depois de registar os URLs de redirecionamento, sob **a concessão Implícita,** selecione as opções **Tokens** de acesso e **fichas de ID** e, em seguida, guarde as suas alterações.

>[!NOTE]
>A autenticação configurante com o Azure Ative Directory para um único sinal só pode ser realizada durante a implantação inicial de um novo cluster AKS. Não é possível configurar um único sinal para um cluster AKS já implantado.

>[!IMPORTANT]
>Se reconfigurar o Azure AD para a autenticação do utilizador utilizando o URI atualizado, limpe a cache do seu navegador para garantir que o token de autenticação atualizado é descarregado e aplicado.

## <a name="grant-permission"></a>Conceder permissão

Cada conta AZure AD deve ser autorizada às APIs apropriadas em Kubernetes para aceder à funcionalidade Dados Ao Vivo (pré-visualização). As etapas para conceder a conta Azure Ative Directory são semelhantes às etapas descritas na secção [de autenticação do RBAC de Kubernetes.](#configure-kubernetes-rbac-authorization) Antes de aplicar o modelo de configuração yaml no seu cluster, substitua **o clusterUser** em **ClusterRoleBinding** com o utilizador pretendido.

>[!IMPORTANT]
>Se o utilizador que concede a ligação NCNC de Kubernetes estiver no mesmo inquilino AZure AD, atribua permissões com base no nome do utilizadorPrincipalName. Se o utilizador estiver num inquilino AD Azure diferente, consultar e utilizar a propriedade objectId.

Para obter ajuda adicional para configurar o cluster AKS **ClusterRoleBinding**, consulte [a ligação do CREATE Kubernetes RBAC](../../aks/azure-ad-integration-cli.md#create-kubernetes-rbac-binding).

## <a name="next-steps"></a>Passos seguintes

Agora que tem a autenticação de configuração, pode ver [métricas,](container-insights-livedata-metrics.md) [Implementações](container-insights-livedata-deployments.md)e [eventos e registos](container-insights-livedata-overview.md) em tempo real a partir do seu cluster.
