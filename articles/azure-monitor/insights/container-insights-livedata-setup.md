---
title: Configurar o Monitor Azure para recipientes Dados Vivos (pré-visualização) / Microsoft Docs
description: Este artigo descreve como configurar a visão em tempo real dos troncos de contentores (stdout/stderr) e eventos sem utilizar kubectl com Monitor Azure para recipientes.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 91f035b98a57fd9a37203cc48b3cc5d685967a13
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251792"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Como configurar a funcionalidade Dados Ao Vivo (pré-visualização)

Para ver os Dados Ao Vivo (pré-visualização) com o Monitor Azure para contentores dos clusters do Serviço Azure Kubernetes (AKS), é necessário configurar a autenticação para conceder permissão ao acesso aos seus dados kubernetes. Esta configuração de segurança permite o acesso em tempo real aos seus dados através da API Kubernetes diretamente no portal Azure.

Esta funcionalidade suporta os seguintes métodos para controlar o acesso aos registos, eventos e métricas:

- AKS sem autorização Kubernetes RBAC ativada
- Ativado com autorização RBAC de Kubernetes do AKS
    - AKS configurado com a função de cluster de ligação  **[clusterMonitoringUser](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS habilitado com um único sinal baseado em SAML baseado em Azure Ative (AD)

Estas instruções requerem tanto o acesso administrativo ao seu cluster Kubernetes, como se configurar para utilizar o Diretório Ativo Azure (AD) para autenticação do utilizador, acesso administrativo à AD Azure.  

Este artigo explica como configurar a autenticação para controlar o acesso à funcionalidade Dados Vivos (pré-visualização) do cluster:

- Controlo de acesso baseado em funções (RBAC) habilitado cluster AKS
- O Cluster Azure Ative Directory integrou o cluster AKS. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esta funcionalidade. Esta funcionalidade baseia-se no acesso direto à API Kubernetes através de um servidor proxy a partir do seu navegador. Permitir a segurança em rede para bloquear a API kubernetes deste proxy bloqueará este tráfego. 

>[!NOTE]
>Esta funcionalidade está disponível em todas as regiões do Azure, incluindo a Azure China. Atualmente, não está disponível no Governo dos EUA.

## <a name="authentication-model"></a>Modelo de autenticação

As funcionalidades De Dados Ao Vivo (pré-visualização) utilizam a API Kubernetes, idêntica à ferramenta de linha de comando `kubectl`. Os pontos finais da Kubernetes API utilizam um certificado auto-assinado, que o seu navegador não poderá validar. Esta funcionalidade utiliza um proxy interno para validar o certificado com o serviço AKS, garantindo que o tráfego é confiável.

O portal Azure solicita-lhe que valide as suas credenciais de login para um cluster de Diretório Ativo Azure, e redirecioná-lo para a configuração de registo do cliente durante a criação do cluster (e reconfigurado neste artigo). Este comportamento é semelhante ao processo de autenticação exigido pela `kubectl`. 

>[!NOTE]
>A autorização para o seu cluster é gerida pela Kubernetes e pelo modelo de segurança com o que está configurado. Os utilizadores que acedem a esta funcionalidade exigem permissão para descarregar a configuração kubernetes *(kubeconfig),* semelhante à execução `az aks get-credentials -n {your cluster name} -g {your resource group}`. Este ficheiro de configuração contém o símbolo de autorização e autenticação para a Função de Utilizador do Cluster de **Serviços Azure Kubernetes,** no caso dos clusters Azure RBAC e AKS sem autorização RBAC ativada. Contém informações sobre os detalhes do registo da AD Azure e do cliente quando o AKS está ativado com um único sinal baseado em SAML baseado em Azure Ative Directory (AD).

>[!IMPORTANT]
>Os utilizadores destas funcionalidades requerem a Função de Utilizador do [Cluster Azure Kubernetes](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) para o cluster, de forma a descarregar o `kubeconfig` e utilizar esta funcionalidade. Os utilizadores **não** necessitam de acesso ao cluster para utilizar esta funcionalidade. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Utilização de clusterMonitoringUser com clusters ativados por RBAC

Para eliminar a necessidade de aplicar alterações adicionais de configuração para permitir que o **cluster** de ligação de regras do utilizador kubernetes aceda à funcionalidade De dados ao vivo (pré-visualização) após permitir a autorização [rBAC,](#configure-kubernetes-rbac-authorization) o AKS adicionou uma nova ligação de cluster Kubernetes chamada **clusterMonitoringUser**. Esta ligação de funções de cluster tem todas as permissões necessárias fora da caixa para aceder à API Kubernetes e os pontos finais para utilizar a funcionalidade Dados Ao Vivo (pré-visualização). 

Para utilizar a funcionalidade Live Data (pré-visualização) com este novo utilizador, é necessário ser membro da função [De Colaborador](../../role-based-access-control/built-in-roles.md#contributor) no recurso de cluster AKS. O Monitor Azure para recipientes, quando ativado, está configurado para autenticar utilizando este utilizador por defeito. Se a ligação de função do clusterMonitoringUser não existir num cluster, o **clusterUser** é utilizado para autenticação.

A AKS lançou este novo papel vinculativo em janeiro de 2020, pelo que os clusters criados antes de janeiro de 2020 não o têm. Se tiver um cluster criado antes de janeiro de 2020, o novo **clusterMonitoringUser** pode ser adicionado a um cluster existente executando uma operação PUT no cluster, ou executando qualquer outra operação no cluster tha executa uma operação PUT no cluster, como atualizar a versão cluster.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster de Kubernetes sem RBAC ativada

Se tiver um cluster do Kubernetes que não está configurado com autorização RBAC de Kubernetes ou integrado com o Azure AD-início de sessão único, não é necessário seguir estes passos. Isto porque tem permissões administrativas por defeito numa configuração não RBAC.

## <a name="configure-kubernetes-rbac-authorization"></a>Configure a autorização RBAC da Kubernetes

Quando ativa a autorização Do Kubernetes RBAC, são utilizados dois utilizadores: **clusterUser** e **clusterAdmin** para aceder à API Kubernetes. Isto é semelhante ao funcionamento `az aks get-credentials -n {cluster_name} -g {rg_name}` sem a opção administrativa. Isto significa que o **clusterUser** precisa de ter acesso aos pontos finais da API kubernetes.

Os passos de exemplo seguintes demonstram como configurar o enlace de função de cluster partir deste modelo de configuração yaml.

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

2. Para atualizar a sua configuração, execute o seguinte comando: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Se tiver aplicado uma versão anterior do ficheiro `LogReaderRBAC.yaml` ao seu cluster, atualize-o copiando e colando o novo código indicado no passo 1 acima e, em seguida, execute o comando indicado no passo 2 para o aplicar ao seu cluster.

## <a name="configure-ad-integrated-authentication"></a>Autenticação integrada em Anúncios 

Um cluster AKS configurado para utilizar o Azure Ative Directory (AD) para autenticação do utilizador utiliza as credenciais de login da pessoa que acede a esta funcionalidade. Nesta configuração, pode iniciar sessão num cluster AKS utilizando o seu símbolo de autenticação Azure AD.

O registo do cliente azure AD deve ser reconfigurado para permitir que o portal Azure redirecione as páginas de autorização como URL de redirecionamento confiável. Os utilizadores de Azure AD têm então acesso direta aos mesmos pontos finais da Kubernetes API através de **ClusterRoles** e **ClusterRoleBindings**. 

Para obter mais informações sobre a configuração avançada de segurança em Kubernetes, reveja a documentação da [Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Se estiver a criar um novo cluster ativado pelo RBAC, consulte o [Integrate Azure Ative Directory com o Serviço Azure Kubernetes](../../aks/azure-ad-integration.md) e siga os passos para configurar a autenticação da AD Azure. Durante as etapas para criar a aplicação do cliente, uma nota nessa secção realça os dois URLs redirecionados que precisa de criar para o Monitor Azure para recipientes que correspondam aos especificados no Passo 3 abaixo.

### <a name="client-registration-reconfiguration"></a>Reconfiguração do registo do cliente

1. Localize o registo do cliente do seu cluster Kubernetes em Azure AD ao abrigo das inscrições do **Azure Ative Directory > App** no portal Azure.

2. **Selecione Autenticação** a partir do painel da mão esquerda. 

3. Adicione dois URLs redirecionais a esta lista como tipos de aplicação **Web.** O valor url da primeira base deve ser `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o valor url da segunda base deve ser `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Se estiver a utilizar esta funcionalidade na Azure China, o valor url da primeira base deve ser `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o valor de URL da segunda base deve ser `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. Depois de registar os URLs redirecionais, sob **a subvenção implícita,** selecione as opções **Fichas** de acesso e **fichas de IDENTIFICAção** e, em seguida, guarde as suas alterações.

>[!NOTE]
>Configurar a autenticação com o Diretório Ativo Azure para um único sinal só pode ser realizado durante a implementação inicial de um novo cluster AKS. Não é possível configurar o início de sessão único para um cluster do AKS já implementado.
  
>[!IMPORTANT]
>Se reconfigurou o Azure AD para autenticação do utilizador utilizando o URI atualizado, limpe a cache do seu navegador para garantir que o token de autenticação atualizado é descarregado e aplicado.

## <a name="grant-permission"></a>Concessão de autorização

Cada conta Azure AD deve ser autorizada às APIs apropriadas em Kubernetes para aceder à funcionalidade Dados Ao Vivo (pré-visualização). As medidas de concessão da conta Azure Ative Directory são semelhantes às etapas descritas na secção de [autenticação RBAC kubernetes.](#configure-kubernetes-rbac-authorization) Antes de aplicar o modelo de configuração do yaml no seu cluster, substitua o **clusterUser** em **ClusterRoleBinding** com o utilizador pretendido. 

>[!IMPORTANT]
>Se o utilizador que concede a ligação RBAC estiver no mesmo inquilino Azure AD, atribua permissões com base no nome principal do utilizador. Se o utilizador estiver num inquilino Azure AD diferente, questione e utilize a propriedade objectId.

Para obter ajuda adicional para configurar o cluster **de cluster AKSRoleBinding,** consulte [Create RBAC binding](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Passos seguintes

Agora que tem a autenticação de configuração, pode ver [métricas,](container-insights-livedata-metrics.md) [implantações](container-insights-livedata-deployments.md)e [eventos e registos](container-insights-livedata-overview.md) em tempo real a partir do seu cluster.
