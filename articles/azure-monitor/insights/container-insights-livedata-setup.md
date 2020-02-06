---
title: Azure Monitor de instalação para contêineres de dados dinâmicos (visualização) | Microsoft Docs
description: Este artigo descreve como configurar a exibição em tempo real de logs de contêiner (stdout/stderr) e eventos sem usar o kubectl com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 1ca52384e5ce657e4fedeb42e3304449a2d6be11
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030693"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>Como configurar o recurso de dados dinâmicos (versão prévia)

Para exibir dados dinâmicos (versão prévia) com Azure Monitor para contêineres de clusters do AKS (serviço kubernetes do Azure), você precisa configurar a autenticação para conceder permissão para acesso aos seus dados do kubernetes. Essa configuração de segurança permite o acesso em tempo real aos seus dados por meio da API kubernetes diretamente no portal do Azure.

Esse recurso dá suporte a três métodos diferentes para controlar o acesso aos logs, eventos e métricas:

- AKS sem autorização Kubernetes RBAC ativada
- Ativado com autorização RBAC de Kubernetes do AKS
- AKS habilitado com logon único baseado em SAML do Azure Active Directory (AD)

Essas instruções exigem o acesso administrativo ao cluster kubernetes e, se configurarem para usar o Azure Active Directory (AD) para autenticação de usuário, acesso administrativo ao Azure AD.  

Este artigo explica como configurar a autenticação para controlar o acesso ao recurso de dados dinâmicos (versão prévia) do cluster:

- O controle de acesso baseado em função (RBAC) habilitou o cluster AKS
- Azure Active Directory cluster AKS integrado. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esta funcionalidade. Esse recurso depende diretamente do acesso à API kubernetes por meio de um servidor proxy do seu navegador. Habilitar a segurança de rede para bloquear a API do kubernetes desse proxy bloqueará esse tráfego. 

>[!NOTE]
>Esse recurso está disponível em todas as regiões do Azure, incluindo o Azure China. No momento, ele não está disponível no Azure no governo dos EUA.

## <a name="authentication-model"></a>Modelo de autenticação

As funcionalidades De Dados Ao Vivo (pré-visualização) utilizam a API Kubernetes, idêntica à ferramenta de linha de comando `kubectl`. Os pontos de extremidade da API do kubernetes utilizam um certificado autoassinado, que o navegador não poderá validar. Esse recurso utiliza um proxy interno para validar o certificado com o serviço AKS, garantindo que o tráfego seja confiável.

O portal do Azure solicita que você valide suas credenciais de logon para um cluster Azure Active Directory e o redireciona para a configuração de registro do cliente durante a criação do cluster (e reconfigurado neste artigo). Este comportamento é semelhante ao processo de autenticação exigido pela `kubectl`. 

>[!NOTE]
>A autorização para o cluster é gerenciada pelo kubernetes e pelo modelo de segurança com o qual ele está configurado. Os utilizadores que acedem a esta funcionalidade exigem permissão para descarregar a configuração kubernetes *(kubeconfig),* semelhante à execução `az aks get-credentials -n {your cluster name} -g {your resource group}`. Este ficheiro de configuração contém o símbolo de autorização e autenticação para a Função de Utilizador do Cluster de **Serviços Azure Kubernetes,** no caso dos clusters Azure RBAC e AKS sem autorização RBAC ativada. Ele contém informações sobre o Azure AD e detalhes de registro de cliente quando o AKS está habilitado com logon único baseado em SAML do Azure Active Directory (AD).

>[!IMPORTANT]
>Os utilizadores destas funcionalidades requerem a Função de Utilizador do [Cluster Azure Kubernetes](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) para o cluster, de forma a descarregar o `kubeconfig` e utilizar esta funcionalidade. Os utilizadores **não** necessitam de acesso ao cluster para utilizar esta funcionalidade. 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster de Kubernetes sem RBAC ativada

Se tiver um cluster do Kubernetes que não está configurado com autorização RBAC de Kubernetes ou integrado com o Azure AD-início de sessão único, não é necessário seguir estes passos. Isso ocorre porque você tem permissões administrativas por padrão em uma configuração não RBAC.

## <a name="configure-kubernetes-rbac-authentication"></a>Configurar a autenticação RBAC kubernetes

Quando ativa a autorização Do Kubernetes RBAC, são utilizados dois utilizadores: **clusterUser** e **clusterAdmin** para aceder à API Kubernetes. Isto é semelhante ao funcionamento `az aks get-credentials -n {cluster_name} -g {rg_name}` sem a opção administrativa. Isto significa que o **clusterUser** tem de ter acesso aos pontos finais da API kubernetes.

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

## <a name="configure-ad-integrated-authentication"></a>Configurar a autenticação integrada do AD 

Um cluster AKS configurado para usar o Azure Active Directory (AD) para autenticação de usuário utiliza as credenciais de logon da pessoa que está acessando esse recurso. Nessa configuração, você pode entrar em um cluster AKS usando o token de autenticação do Azure AD.

O registro de cliente do Azure AD deve ser reconfigurado para permitir que o portal do Azure redirecione páginas de autorização como uma URL de redirecionamento confiável. Os utilizadores de Azure AD têm então acesso direta aos mesmos pontos finais da Kubernetes API através de **ClusterRoles** e **ClusterRoleBindings**. 

Para obter mais informações sobre a configuração avançada de segurança em Kubernetes, reveja a documentação da [Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Se estiver a criar um novo cluster ativado pelo RBAC, consulte o [Integrate Azure Ative Directory com o Serviço Azure Kubernetes](../../aks/azure-ad-integration.md) e siga os passos para configurar a autenticação da AD Azure. Durante as etapas para criar a aplicação do cliente, uma nota nessa secção realça os dois URLs redirecionados que precisa de criar para o Monitor Azure para recipientes que correspondam aos especificados no Passo 3 abaixo.

### <a name="client-registration-reconfiguration"></a>Reconfiguração de registro de cliente

1. Localize o registo do cliente do seu cluster Kubernetes em Azure AD ao abrigo das inscrições do **Azure Ative Directory > App** no portal Azure.

2. **Selecione Autenticação** a partir do painel da mão esquerda. 

3. Adicione dois URLs redirecionais a esta lista como tipos de aplicação **Web.** O valor url da primeira base deve ser `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o valor url da segunda base deve ser `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Se estiver a utilizar esta funcionalidade na Azure China, o valor url da primeira base deve ser `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o valor de URL da segunda base deve ser `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. Depois de registar os URLs redirecionais, em **definições avançadas,** selecione as opções **Fichas** de acesso e **fichas de ID** e, em seguida, guarde as suas alterações.

>[!NOTE]
>A configuração da autenticação com o Azure Active Directory para logon único só pode ser realizada durante a implantação inicial de um novo cluster AKS. Não é possível configurar o início de sessão único para um cluster do AKS já implementado.
  
>[!IMPORTANT]
>Se você reconfigurou o Azure AD para autenticação de usuário usando o URI atualizado, limpe o cache do navegador para garantir que o token de autenticação atualizado seja baixado e aplicado.

## <a name="grant-permission"></a>Conceder permissão

Cada conta do Azure AD deve receber permissão para as APIs apropriadas no kubernetes para acessar o recurso de dados dinâmicos (versão prévia). As medidas de concessão da conta Azure Ative Directory são semelhantes às etapas descritas na secção de [autenticação RBAC kubernetes.](#configure-kubernetes-rbac-authentication) Antes de aplicar o modelo de configuração do yaml no seu cluster, substitua o **clusterUser** em **ClusterRoleBinding** com o utilizador pretendido. 

>[!IMPORTANT]
>Se o usuário para o qual você concede a associação de RBAC estiver no mesmo locatário do Azure AD, atribua permissões com base no userPrincipalName. Se o usuário estiver em um locatário diferente do Azure AD, consulte e use a propriedade objectId.

Para obter ajuda adicional para configurar o cluster **de cluster AKSRoleBinding,** consulte [Create RBAC binding](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Passos seguintes

Agora que tem a autenticação de configuração, pode ver [métricas,](container-insights-livedata-metrics.md) [implantações](container-insights-livedata-deployments.md)e [eventos e registos](container-insights-livedata-overview.md) em tempo real a partir do seu cluster.
