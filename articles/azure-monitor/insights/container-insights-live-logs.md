---
title: Vista do Azure Monitor para contentores logs em tempo real | Documentos da Microsoft
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
ms.openlocfilehash: 2eab6fa75e4adbbde7bcf20f18301a1e516235c2
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035356"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Como exibir logs e eventos em tempo real (visualização)
Azure Monitor para contêineres inclui um recurso, que está atualmente em versão prévia, que fornece uma exibição dinâmica em seus logs de contêiner (stdout/stderr) do AKS (serviço kubernetes do Azure) e eventos sem a necessidade de executar comandos kubectl. Quando você seleciona uma das opções, um novo painel é exibido abaixo da tabela de dados de desempenho na exibição **nós**, **controladores**e **contêineres** . Ele mostra o log ao vivo e eventos gerados pelo mecanismo de contêiner para auxiliar ainda mais na solução de problemas em tempo real.

>[!NOTE]
>O acesso de **colaborador** ao recurso de cluster é necessário para que esse recurso funcione.
>

Os logs ao vivo dão suporte a três métodos diferentes para controlar o acesso aos logs:

1. AKS sem autorização Kubernetes RBAC ativada
2. Ativado com autorização RBAC de Kubernetes do AKS
3. AKS habilitado com logon único baseado em SAML do Azure Active Directory (AD)

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster de Kubernetes sem RBAC ativada
 
Se tiver um cluster do Kubernetes que não está configurado com autorização RBAC de Kubernetes ou integrado com o Azure AD-início de sessão único, não é necessário seguir estes passos. Como a autorização kubernetes usa as permissões Kube-API, somente leitura são necessárias.

## <a name="kubernetes-rbac-authorization"></a>Autorização RBAC do Kubernetes
Se tiver ativado o autorização RBAC do Kubernetes, terá de aplicar o enlace de função de cluster. Os passos de exemplo seguintes demonstram como configurar o enlace de função de cluster partir deste modelo de configuração yaml. 

1. Copie e cole o ficheiro yaml e guarde-o como LogReaderRBAC.yaml.  

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

2. Se você estiver configurando-o pela primeira vez, aplique a associação de regra de cluster executando o seguinte `kubectl create -f LogReaderRBAC.yaml`comando:. Se você habilitou anteriormente o suporte para visualização de logs ao vivo antes de introduzirmos logs de eventos ao vivo, para atualizar sua `kubectl apply -f LogReaderRBAC.yaml`configuração, execute o seguinte comando:.

## <a name="configure-aks-with-azure-active-directory"></a>Configurar o AKS com o Azure Active Directory

AKS pode ser configurado para utilizar o Azure Active Directory (AD) para autenticação de utilizador. Se você estiver configurando-o pela primeira vez, consulte [integrar Azure Active Directory ao serviço kubernetes do Azure](../../aks/azure-ad-integration.md). Durante as etapas para criar o [aplicativo cliente](../../aks/azure-ad-integration.md#create-the-client-application), especifique o seguinte:

-  **URI**de redirecionamento: Dois tipos de aplicativos **Web** precisam ser criados. O primeiro valor da URL base deve `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` ser e o segundo valor da URL base `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`deve ser.
- Depois de registrar o aplicativo, na página **visão geral** , selecione **autenticação** no painel esquerdo. Na página **autenticação** , em **Configurações avançadas** , conceda implicitamente tokens de **acesso** e tokens de **ID** e salve as alterações.

>[!NOTE]
>A configuração da autenticação com o Azure Active Directory para logon único só pode ser realizada durante a implantação inicial de um novo cluster AKS. Não é possível configurar o início de sessão único para um cluster do AKS já implementado.
  
>[!IMPORTANT]
>Se você reconfigurou o Azure AD para autenticação de usuário usando o URI atualizado, limpe o cache do navegador para garantir que o token de autenticação atualizado seja baixado e aplicado.   

## <a name="view-live-logs-and-events"></a>Exibir eventos e logs ao vivo

Você pode exibir eventos de log em tempo real à medida que eles são gerados pelo mecanismo de contêiner do modo de exibição **nós**, **controladores**e **contêineres** . No painel Propriedades, você seleciona a opção **exibir dados dinâmicos (versão prévia)** e um painel é apresentado abaixo da tabela de dados de desempenho, na qual você pode exibir o log e os eventos em um fluxo contínuo.

![Painel Propriedades do nó exibir opção de logs dinâmicos](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

As mensagens de log e de evento são limitadas com base em qual tipo de recurso está selecionado na exibição.

| Vista | Tipo de recurso | Log ou evento | Dados apresentados |
|------|---------------|--------------|----------------|
| Nós | Nó | Evento | Quando um nó é selecionado, os eventos não são filtrados e mostram eventos de kubernetes em todo o cluster. O título do painel mostra o nome do cluster. |
| Nós | Pod | Evento | Quando um pod é selecionado, os eventos são filtrados para seu namespace. O título do painel mostra o namespace do pod. | 
| Controladores | Pod | Evento | Quando um pod é selecionado, os eventos são filtrados para seu namespace. O título do painel mostra o namespace do pod. |
| Controladores | Controlador | Evento | Quando um controlador é selecionado, os eventos são filtrados para seu namespace. O título do painel mostra o namespace do controlador. |
| Nós/controladores/contêineres | Contentor | Registos | O título do painel mostra o nome do pod com o qual o contêiner é agrupado. |

Se o cluster do AKS é configurado com SSO com o AAD, lhe for pedido para se autenticar na primeira utilização durante a sessão do browser. Selecione a sua conta e a autenticação completa com o Azure.  

Após a autenticação bem-sucedida, o painel de registo em direto será apresentada na seção na parte inferior do painel do meio. Se o indicador de estado de obtenção mostra uma marca de verificação verde, o que é mais à direita do painel, significa que este possa obter dados.
    
  ![Dados recuperados do painel de logs dinâmicos](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na barra de pesquisa, você pode filtrar por palavra-chave para realçar o texto no log ou evento e, na barra de pesquisa à direita, mostra quantos resultados correspondem ao filtro.

  ![Exemplo de filtro do painel de registos do Live](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Ao exibir eventos, você pode limitar os resultados usando o **filtro** Pill encontrado à direita da barra de pesquisa. Dependendo do recurso que você selecionou, o Pill lista um pod, um namespace ou um cluster do qual escolher.  

Para suspender o AutoScroll e controlar o comportamento do painel e permitir que você role manualmente os novos dados lidos, clique na opção de rolagem. Para reabilitar o AutoScroll, basta clicar na opção de rolagem novamente. Você também pode pausar a recuperação de dados de log ou de evento clicando na opção **Pause** e, quando estiver pronto para retomar, basta clicar em **Play**.  

![Vista em direto de colocar em pausa do painel de logs dinâmicos](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Você pode acessar os logs de Azure Monitor para ver os logs de contêiner históricos selecionando **Exibir logs de contêiner** no modo de exibição de lista suspensa **no Analytics**.

## <a name="next-steps"></a>Passos Seguintes

- Para continuar a aprender a utilizar o Azure Monitor e monitorizar outros aspectos do seu cluster do AKS, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.
