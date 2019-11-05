---
title: Azure Monitor de instalação para contêineres de dados dinâmicos (visualização) | Microsoft Docs
description: Este artigo descreve como configurar a exibição em tempo real de logs de contêiner (stdout/stderr) e eventos sem usar o kubectl com Azure Monitor para contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2019
ms.author: magoedte
ms.openlocfilehash: 596c5ad378d471c6c98616a48f44e96c365ee0bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514371"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>Como configurar o recurso de dados dinâmicos (versão prévia)

Para exibir dados dinâmicos (versão prévia) com Azure Monitor para contêineres de clusters do AKS (serviço kubernetes do Azure), você precisa configurar a autenticação para conceder permissão para acesso aos seus dados do kubernetes. Essa configuração de segurança permite o acesso em tempo real aos seus dados por meio da API kubernetes diretamente no portal do Azure.

Esse recurso dá suporte a três métodos diferentes para controlar o acesso aos logs, eventos e métricas:

- AKS sem autorização de RBAC kubernetes habilitada
- AKS habilitado com autorização de RBAC do kubernetes
- AKS habilitado com logon único baseado em SAML do Azure Active Directory (AD)

Essas instruções exigem o acesso administrativo ao cluster kubernetes e, se configurarem para usar o Azure Active Directory (AD) para autenticação de usuário, acesso administrativo ao Azure AD.  

Este artigo explica como configurar a autenticação para controlar o acesso ao recurso de dados dinâmicos (versão prévia) do cluster:

- O controle de acesso baseado em função (RBAC) habilitou o cluster AKS
- Azure Active Directory cluster AKS integrado. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não têm suporte com esse recurso. Esse recurso depende diretamente do acesso à API kubernetes por meio de um servidor proxy do seu navegador. Habilitar a segurança de rede para bloquear a API do kubernetes desse proxy bloqueará esse tráfego. 

>[!NOTE]
>Esse recurso está disponível em todas as regiões do Azure, incluindo o Azure China. No momento, ele não está disponível no Azure no governo dos EUA.

## <a name="authentication-model"></a>Modelo de autenticação

Os recursos de dados dinâmicos (versão prévia) utilizam a API kubernetes, idêntica à ferramenta de linha de comando `kubectl`. Os pontos de extremidade da API do kubernetes utilizam um certificado autoassinado, que o navegador não poderá validar. Esse recurso utiliza um proxy interno para validar o certificado com o serviço AKS, garantindo que o tráfego seja confiável.

O portal do Azure solicita que você valide suas credenciais de logon para um cluster Azure Active Directory e o redireciona para a configuração de registro do cliente durante a criação do cluster (e reconfigurado neste artigo). Esse comportamento é semelhante ao processo de autenticação exigido pelo `kubectl`. 

>[!NOTE]
>A autorização para o cluster é gerenciada pelo kubernetes e pelo modelo de segurança com o qual ele está configurado. Os usuários que acessam esse recurso exigem permissão para baixar a configuração do kubernetes (*kubeconfig*), semelhante à execução de `az aks get-credentials -n {your cluster name} -g {your resource group}`. Esse arquivo de configuração contém o token de autorização e autenticação para a **função de usuário de cluster do serviço kubernetes do Azure**, no caso de clusters habilitados para RBAC do Azure e do AKS sem autorização de RBAC habilitada. Ele contém informações sobre o Azure AD e detalhes de registro de cliente quando o AKS está habilitado com logon único baseado em SAML do Azure Active Directory (AD).

>[!IMPORTANT]
>Os usuários desses recursos exigem a [função de usuário do cluster kubernetes do Azure](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) para o cluster a fim de baixar o `kubeconfig` e usar esse recurso. Os usuários **não** exigem acesso de colaborador ao cluster para utilizar esse recurso. 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster kubernetes sem RBAC habilitado

Se você tiver um cluster kubernetes que não esteja configurado com a autorização do RBAC do kubernetes ou integrado ao logon único do Azure AD, não será necessário seguir estas etapas. Isso ocorre porque você tem permissões administrativas por padrão em uma configuração não RBAC.

## <a name="configure-kubernetes-rbac-authentication"></a>Configurar a autenticação RBAC kubernetes

Quando você habilita a autorização do RBAC kubernetes, dois usuários são utilizados: **clusterUser** e **clusterAdmin** para acessar a API do kubernetes. Isso é semelhante à execução de `az aks get-credentials -n {cluster_name} -g {rg_name}` sem a opção administrativa. Isso significa que o **clusterUser** precisa receber acesso aos pontos de extremidade na API do kubernetes.

As etapas de exemplo a seguir demonstram como configurar a associação de função de cluster a partir deste modelo de configuração YAML.

1. Copie e cole o arquivo YAML e salve-o como LogReaderRBAC. YAML.  

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

2. Para atualizar sua configuração, execute o seguinte comando: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Se você aplicou uma versão anterior do arquivo de `LogReaderRBAC.yaml` ao cluster, atualize-o copiando e colando o novo código mostrado na etapa 1 acima e, em seguida, execute o comando mostrado na etapa 2 para aplicá-lo ao cluster.

## <a name="configure-ad-integrated-authentication"></a>Configurar a autenticação integrada do AD 

Um cluster AKS configurado para usar o Azure Active Directory (AD) para autenticação de usuário utiliza as credenciais de logon da pessoa que está acessando esse recurso. Nessa configuração, você pode entrar em um cluster AKS usando o token de autenticação do Azure AD.

O registro de cliente do Azure AD deve ser reconfigurado para permitir que o portal do Azure redirecione páginas de autorização como uma URL de redirecionamento confiável. Os usuários do Azure AD recebem acesso diretamente aos mesmos pontos de extremidade da API do kubernetes por meio de **ClusterRoles** e **ClusterRoleBindings**. 

Para obter mais informações sobre a configuração de segurança avançada no kubernetes, consulte a [documentação do kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Se você estiver criando um novo cluster habilitado para RBAC, consulte [integrar Azure Active Directory com o serviço kubernetes do Azure](../../aks/azure-ad-integration.md) e siga as etapas para configurar a autenticação do Azure AD. Durante as etapas para criar o aplicativo cliente, uma observação nessa seção realça as duas URLs de redirecionamento que você precisa criar para Azure Monitor para contêineres. 

### <a name="client-registration-reconfiguration"></a>Reconfiguração de registro de cliente

1. Localize o registro de cliente para o cluster kubernetes no Azure AD em **Azure Active Directory > registros de aplicativo** no portal do Azure.

2. Selecione **autenticação** no painel esquerdo. 

3. Adicione duas URLs de redirecionamento a essa lista como tipos de aplicativos **Web** . O primeiro valor da URL base deve ser `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o segundo valor da URL base deve ser `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Se você estiver usando esse recurso no Azure China, o primeiro valor da URL base deverá ser `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o segundo valor da URL base deverá ser `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. Depois de registrar as URLs de redirecionamento, em **Configurações avançadas**, selecione as opções **tokens de acesso** e **tokens de ID** e salve as alterações.

>[!NOTE]
>A configuração da autenticação com o Azure Active Directory para logon único só pode ser realizada durante a implantação inicial de um novo cluster AKS. Você não pode configurar o logon único para um cluster AKS já implantado.
  
>[!IMPORTANT]
>Se você reconfigurou o Azure AD para autenticação de usuário usando o URI atualizado, limpe o cache do navegador para garantir que o token de autenticação atualizado seja baixado e aplicado.

## <a name="grant-permission"></a>Conceder permissão

Cada conta do Azure AD deve receber permissão para as APIs apropriadas no kubernetes para acessar o recurso de dados dinâmicos (versão prévia). As etapas para conceder a conta de Azure Active Directory são semelhantes às etapas descritas na seção [autenticação RBAC kubernetes](#configure-kubernetes-rbac-authentication) . Antes de aplicar o modelo de configuração YAML ao cluster, substitua **clusterUser** em **ClusterRoleBinding** pelo usuário desejado. 

>[!IMPORTANT]
>Se o usuário para o qual você concede a associação de RBAC estiver no mesmo locatário do Azure AD, atribua permissões com base no userPrincipalName. Se o usuário estiver em um locatário diferente do Azure AD, consulte e use a propriedade objectId.

Para obter ajuda adicional sobre como configurar o **ClusterRoleBinding**do cluster AKs, consulte [criar Associação RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Passos seguintes

Agora que você tem a autenticação de instalação, é possível exibir as [métricas](container-insights-livedata-metrics.md), as [implantações](container-insights-livedata-deployments.md)e [os eventos e os logs](container-insights-livedata-overview.md) em tempo real do cluster.