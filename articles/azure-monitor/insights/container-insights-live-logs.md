---
title: Exibir Azure Monitor de logs de contêineres em tempo real | Microsoft Docs
description: Este artigo descreve a exibição em tempo real de logs de contêiner (stdout/stderr) e eventos sem usar kubectl com Azure Monitor para contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: d947b44177e9aa5777d759286d982e974e378497
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389778"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Como exibir logs e eventos em tempo real (visualização)
Azure Monitor para contêineres inclui um recurso, que está atualmente em versão prévia, que fornece uma exibição dinâmica em seus logs de contêiner (stdout/stderr) do AKS (serviço kubernetes do Azure) e eventos sem a necessidade de executar comandos kubectl. Quando você seleciona uma das opções, um novo painel é exibido abaixo da tabela de dados de desempenho na exibição **nós**, **controladores**e **contêineres** . Ele mostra o log ao vivo e eventos gerados pelo mecanismo de contêiner para auxiliar ainda mais na solução de problemas em tempo real.

>[!NOTE]
>Esse recurso está disponível em todas as regiões do Azure, incluindo o Azure China. No momento, ele não está disponível no Azure no governo dos EUA.

>[!NOTE]
>O acesso à **função de usuário de cluster do serviço kubernetes do Azure** para o recurso de cluster é necessário para que esse recurso funcione. [Saiba mais sobre a função de usuário do cluster kubernetes do Azure](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions).

Os logs ao vivo dão suporte a três métodos diferentes para controlar o acesso aos logs:

1. AKS sem autorização de RBAC kubernetes habilitada
2. AKS habilitado com autorização de RBAC do kubernetes
3. AKS habilitado com logon único baseado em SAML do Azure Active Directory (AD)

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster kubernetes sem RBAC habilitado
 
Se você tiver um cluster kubernetes que não esteja configurado com a autorização do RBAC do kubernetes ou integrado ao logon único do Azure AD, não será necessário seguir estas etapas. Como a autorização kubernetes usa as permissões Kube-API, somente leitura são necessárias.

## <a name="kubernetes-rbac-authorization"></a>Autorização do RBAC kubernetes
Se você tiver habilitado a autorização do RBAC kubernetes, será necessário aplicar a associação de função de cluster. As etapas de exemplo a seguir demonstram como configurar a associação de função de cluster a partir deste modelo de configuração YAML. 

1. Copie e cole o arquivo YAML e salve-o como LogReaderRBAC. YAML.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
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

2. Se você estiver configurando-o pela primeira vez, aplique a associação de regra de cluster executando o seguinte comando: `kubectl create -f LogReaderRBAC.yaml`. Se você habilitou anteriormente o suporte para visualização de logs ao vivo antes de introduzirmos logs de eventos ao vivo, para atualizar sua configuração, execute o seguinte comando: `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>Configurar o AKS com Azure Active Directory

AKS pode ser configurado para usar o Azure Active Directory (AD) para autenticação de usuário. Se você estiver configurando-o pela primeira vez, consulte [integrar Azure Active Directory ao serviço kubernetes do Azure](../../aks/azure-ad-integration.md). Durante as etapas para criar o [aplicativo cliente](../../aks/azure-ad-integration.md#create-the-client-application), especifique o seguinte:

-  **URI de redirecionamento**: dois tipos de aplicativos **Web** precisam ser criados. O primeiro valor da URL base deve ser `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o segundo valor da URL base deve ser `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Depois de registrar o aplicativo, na página **visão geral** , selecione **autenticação** no painel esquerdo. Na página **autenticação** , em **Configurações avançadas** , conceda implicitamente **tokens de acesso** e **tokens de ID** e salve as alterações.

>[!NOTE]
>Se você estiver usando esse recurso na região do Azure na China, o primeiro valor da URL base deverá ser `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` e o segundo valor da URL base deverá ser `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

>[!NOTE]
>A configuração da autenticação com o Azure Active Directory para logon único só pode ser realizada durante a implantação inicial de um novo cluster AKS. Você não pode configurar o logon único para um cluster AKS já implantado.
  
>[!IMPORTANT]
>Se você reconfigurou o Azure AD para autenticação de usuário usando o URI atualizado, limpe o cache do navegador para garantir que o token de autenticação atualizado seja baixado e aplicado.   

## <a name="view-live-logs-and-events"></a>Exibir eventos e logs ao vivo

Você pode exibir eventos de log em tempo real à medida que eles são gerados pelo mecanismo de contêiner do modo de exibição **nós**, **controladores**e **contêineres** . No painel Propriedades, você seleciona a opção **exibir dados dinâmicos (versão prévia)** e um painel é apresentado abaixo da tabela de dados de desempenho, na qual você pode exibir o log e os eventos em um fluxo contínuo.

![Painel Propriedades do nó exibir opção de logs dinâmicos](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

As mensagens de log e de evento são limitadas com base em qual tipo de recurso está selecionado na exibição.

| Ver | Tipo de recurso | Log ou evento | Dados apresentados |
|------|---------------|--------------|----------------|
| Nós | Nó | Evento | Quando um nó é selecionado, os eventos não são filtrados e mostram eventos de kubernetes em todo o cluster. O título do painel mostra o nome do cluster. |
| Nós | Pod | Evento | Quando um pod é selecionado, os eventos são filtrados para seu namespace. O título do painel mostra o namespace do pod. | 
| Controladores | Pod | Evento | Quando um pod é selecionado, os eventos são filtrados para seu namespace. O título do painel mostra o namespace do pod. |
| Controladores | Controle | Evento | Quando um controlador é selecionado, os eventos são filtrados para seu namespace. O título do painel mostra o namespace do controlador. |
| Nós/controladores/contêineres | Contentor | Registos | O título do painel mostra o nome do pod com o qual o contêiner é agrupado. |

Se o cluster AKS estiver configurado com SSO usando o AAD, você será solicitado a autenticar no primeiro uso durante a sessão do navegador. Selecione sua conta e conclua a autenticação com o Azure.  

Após a autenticação bem-sucedida, o painel log ao vivo aparecerá na seção inferior do painel central. Se o indicador de status de busca mostrar uma marca de seleção verde, que está na extrema direita do painel, isso significa que ele pode recuperar dados.
    
  ![O painel de logs dinâmicos recuperou dados](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na barra de pesquisa, você pode filtrar por palavra-chave para realçar o texto no log ou evento e, na barra de pesquisa à direita, mostra quantos resultados correspondem ao filtro.

  ![Exemplo de filtro de painel de logs ao vivo](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Ao exibir eventos, você pode limitar os resultados usando o **filtro** Pill encontrado à direita da barra de pesquisa. Dependendo do recurso que você selecionou, o Pill lista um pod, um namespace ou um cluster do qual escolher.  

Para suspender o AutoScroll e controlar o comportamento do painel e permitir que você role manualmente os novos dados lidos, clique na opção de **rolagem** . Para reabilitar o AutoScroll, basta clicar na opção de **rolagem** novamente. Você também pode pausar a recuperação de dados de log ou de evento clicando na opção **Pause** e, quando estiver pronto para retomar, basta clicar em **Play**.  

![Painel de logs dinâmicos pausar exibição dinâmica](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Você pode acessar os logs de Azure Monitor para ver os logs de contêiner históricos selecionando **Exibir logs de contêiner** no modo de exibição de lista suspensa **no Analytics**.

## <a name="next-steps"></a>Passos seguintes

- Para continuar aprendendo a usar Azure Monitor e monitorar outros aspectos do cluster AKS, consulte Exibir a [integridade do serviço kubernetes do Azure](container-insights-analyze.md).

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.
