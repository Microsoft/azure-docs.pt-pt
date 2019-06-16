---
title: Vista do Azure Monitor para contentores logs em tempo real | Documentos da Microsoft
description: Este artigo descreve a vista em tempo real de registos de contentor (stdout/stderr) e eventos sem utilizar o kubectl com o Azure Monitor para contentores.
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
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: 71c6f1936f8cbc700a24d0ffb497947c8c8d3a50
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075314"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Como ver registos e eventos em tempo real (pré-visualização)
Monitor do Azure para contentores inclui um recurso, o que está atualmente em pré-visualização, que fornece uma vista em direto para seus eventos e registos de contentor do Azure Kubernetes Service (AKS) (stdout/stderr) sem ter de executar os comandos de kubectl. Quando seleciona uma destas opções, um novo painel é apresentada abaixo da tabela de dados de desempenho no **nós**, **controladores**, e **contentores** vista. Ele mostra o registo em direto e os eventos gerados pelo mecanismo de contentor para auxiliar mais ainda na resolução de problemas em tempo real.

>[!NOTE]
>**Contribuinte** acesso ao recurso de cluster é necessário para esta funcionalidade funcione.
>

Três métodos diferentes para controlar o acesso para os registos de suporte de logs dinâmicos:

1. AKS sem autorização Kubernetes RBAC ativada
2. Ativado com autorização RBAC de Kubernetes do AKS
3. AKS ativada com o Azure Active Directory (AD) baseado em SAML início de sessão único na

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster de Kubernetes sem RBAC ativada
 
Se tiver um cluster do Kubernetes que não está configurado com autorização RBAC de Kubernetes ou integrado com o Azure AD-início de sessão único, não é necessário seguir estes passos. Uma vez que a autorização de Kubernetes utiliza a api do kube, são necessárias permissões de só de leitura.

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

2. Se estiver a configurá-lo pela primeira vez, criar o enlace de regra de cluster executando o seguinte comando: `kubectl create -f LogReaderRBAC.yaml`. Se ativou anteriormente o suporte para visualizar os registos em direto antes de introduzimos registos de eventos em direto, para atualizar a configuração, execute o seguinte comando: `kubectl apply -f LogReaderRBAC.yml`.

## <a name="configure-aks-with-azure-active-directory"></a>Configurar o AKS com o Azure Active Directory

AKS pode ser configurado para utilizar o Azure Active Directory (AD) para autenticação de utilizador. Se estiver a configurá-lo pela primeira vez, consulte [integrar o Azure Active Directory com o Azure Kubernetes Service](../../aks/azure-ad-integration.md). Durante os passos para criar o [aplicação de cliente](../../aks/azure-ad-integration.md#create-the-client-application), especifique o seguinte:

- **(Opcional) do URI de redirecionamento**: Este é um **Web** tipo de aplicação e o valor de URL base devem ser `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Após o Registro do aplicativo, a partir do **descrição geral** página select **autenticação** no painel esquerdo. Sobre o **autenticação** página, em **definições avançadas** implicitamente conceder **tokens de acesso** e **tokens de ID** e, em seguida, guarde sua alterações.

>[!NOTE]
>Configuração da autenticação no Azure Active Directory para início de sessão único só pode ser feito durante a implementação inicial de um novo cluster do AKS. Não é possível configurar o início de sessão único para um cluster do AKS já implementado.

## <a name="view-live-logs-and-events"></a>Ver registos em direto e eventos

Pode ver eventos de registo em tempo real à medida que são gerados pelo mecanismo do contentor do **nós**, **controladores**, e **contentores** vista. A partir do painel de propriedades, pode selecionar **ver dados ao vivo (pré-visualização)** opção e um painel que é apresentada abaixo da tabela de dados de desempenho onde pode ver o registo e eventos num fluxo contínuo.

![Opção de logs dinâmicos de modo de exibição de painel de propriedades de nó](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Mensagens de registos e eventos são limitadas com base em que tipo de recurso é selecionado na vista.

| Vista | Tipo de recurso | Registo ou um evento | Dados apresentados |
|------|---------------|--------------|----------------|
| Nós | Nó | Evento | Quando é selecionado um nó de eventos não são filtrados e mostram eventos de Kubernetes em todo o cluster. O título do painel mostra o nome do cluster. |
| Nós | Pod | Evento | Quando é selecionado um pod eventos são filtrados ao seu espaço de nomes. O título do painel mostra o espaço de nomes do pod. | 
| Controladores | Pod | Evento | Quando é selecionado um pod eventos são filtrados ao seu espaço de nomes. O título do painel mostra o espaço de nomes do pod. |
| Controladores | controlador | Evento | Quando é selecionado um controlador de eventos são filtrados ao seu espaço de nomes. O título do painel mostra o espaço de nomes do controlador. |
| Nós/controladores/contentores | Contentor | Registos | O título do painel mostra que o nome do pod o contentor está agrupado com. |

Se o cluster do AKS é configurado com SSO com o AAD, lhe for pedido para se autenticar na primeira utilização durante a sessão do browser. Selecione a sua conta e a autenticação completa com o Azure.  

Após a autenticação bem-sucedida, o painel de registo em direto será apresentada na seção na parte inferior do painel do meio. Se o indicador de estado de obtenção mostra uma marca de verificação verde, o que é mais à direita do painel, significa que este possa obter dados.
    
  ![Dados recuperados do painel de logs dinâmicos](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na barra de pesquisa, pode filtrar por palavra-chave para realçar o texto no log ou eventos e na barra de pesquisa na extremidade direita, mostra quantos resultados corresponderem a terminar o filtro.

  ![Exemplo de filtro do painel de registos do Live](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Ao visualizar eventos, pode adicionalmente limitar os resultados usando a **filtro** envenenadas encontrada para a direita da barra de pesquisa. Dependendo de quais recursos que selecionou, a envenenadas lista um pod, o espaço de nomes ou o cluster para escolher a partir de.  

Para suspender autoscroll e controlar o comportamento do painel e permitem-lhe manualmente percorrer os novos dados de leitura, clique nas **rolagem** opção. Para voltar a activar autoscroll, basta clicar o **rolagem** opção novamente. Também pode interromper a obtenção de dados de log ou eventos, clicando no **colocar em pausa** opção e quando estiver pronto para ser retomar, basta clicar **reproduzir**.  

![Vista em direto de colocar em pausa do painel de logs dinâmicos](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Pode aceder a registos de Monitor do Azure para ver registos de contentor históricos selecionando **ver registos de contentor** na lista pendente **ver na análise**.

## <a name="next-steps"></a>Passos Seguintes
- Para continuar a aprender a utilizar o Azure Monitor e monitorizar outros aspectos do seu cluster do AKS, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
- Modo de exibição [exemplos de consulta de registo](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para os alertas, visualizar ou analisar os seus clusters.
