---
title: Vista do Azure Monitor para contentores logs em tempo real | Documentos da Microsoft
description: Este artigo descreve a vista em tempo real de registos de contentor (stdout/stderr) sem utilizar o kubectl com o Azure Monitor para contentores.
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
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: 6fe8cccf60e60ada34e3b7847964958cf6e03c4a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904751"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Como ver o contentor registos em tempo real com o Azure Monitor para contentores (pré-visualização)
Esta funcionalidade, que está atualmente em pré-visualização, fornece uma vista em tempo real sobre os seus registos de contentor do Azure Kubernetes Service (AKS) (stdout/stderr) sem ter de executar os comandos de kubectl. Quando seleciona esta opção, o novo painel é apresentada abaixo da tabela de dados de desempenho de contentores no **contentores** vista.  Mostra o registo em direto gerado pelo mecanismo de contentor para auxiliar mais ainda na resolução de problemas em tempo real. **Contribuinte** acesso ao recurso de cluster é necessário para esta funcionalidade funcione.

Live registos suporta três diferentes métodos para controlar o acesso para os registos:

1. AKS sem autorização Kubernetes RBAC ativada 
2. Ativado com autorização RBAC de Kubernetes do AKS
3. AKS ativada com o Azure Active Directory (AD) SAML início de sessão único com base em 

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
        resources: ["pods/log"] 
        verbs: ["get"] 
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

2. Criar o enlace de regra de cluster executando o seguinte comando: `kubectl create -f LogReaderRBAC.yaml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Configurar o AKS com o Azure Active Directory
AKS pode ser configurado para utilizar o Azure Active Directory (AD) para autenticação de utilizador. Se estiver a configurar isso pela primeira vez, consulte [integrar o Azure Active Directory com o Azure Kubernetes Service](../../aks/aad-integration.md). Durante os passos para criar o [aplicação de cliente](../../aks/aad-integration.md#create-client-application) e especifique a **URI de redirecionamento**, precisa adicionar noutro URI para a lista `https://ininprodeusuxbase.microsoft.com/*`.  

>[!NOTE]
>Configuração da autenticação no Azure Active Directory para início de sessão único só pode ser realizada durante o implementação de um novo cluster do AKS. Não é possível configurar o início de sessão único para um cluster do AKS já implementado.  
> 

## <a name="view-live-logs"></a>Ver registos em direto
Quando está a visualizar **contentores**, pode **registos Zobrazit Kontejner** ou **ver registos de contentor em direto**.  Quando seleciona **logs dinâmicos de contentor do modo de exibição**, um novo painel apresentado abaixo da tabela de dados de desempenho de contentores e que mostra o registo em direto gerado pelo mecanismo de contentor para auxiliar mais ainda na resolução de problemas em tempo real.  
1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Partir do **Microsoft Azure** menu, selecione **Monitor** e, em seguida, selecione **contentores**.  
3. Selecione um contentor a partir da lista sob a **monitorizados contentores** vista.  
4. Selecione o **contentores** vista e no painel de propriedades para um contentor selecionado, o link **ver registos de contentor em direto** está listado.  
5. Se o cluster do AKS é configurado com SSO com o AAD, lhe for pedido para se autenticar na primeira utilização durante a sessão do browser. Selecione a sua conta e a autenticação completa com o Azure.  

Após a autenticação bem-sucedida, o painel de registo em direto será apresentada na seção na parte inferior do painel do meio. Se o indicador de estado de obtenção mostra uma marca de verificação verde, o que é mais à direita do painel, significa que este possa obter dados.
    
  ![Dados recuperados do painel de logs dinâmicos](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na barra de pesquisa, pode filtrar por palavra-chave para realçar o texto no registo.   

  ![Exemplo de filtro do painel de registos do Live](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

Para suspender a rolagem automática e controlar o comportamento do painel e permitem-lhe manualmente percorrer os novos dados de registo de leitura, clique nas **rolagem** opção.  Para voltar a ativar o deslocamento automático, basta clicar o **rolagem** opção novamente.  Também pode interromper a obtenção de dados de registo clicando no **colocar em pausa** opção e quando estiver pronto para ser retomar, basta clicar **reproduzir**.  

![Vista em direto de colocar em pausa do painel de logs dinâmicos](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>Passos Seguintes
Para continuar a aprender a utilizar o Azure Monitor e monitorizar outros aspectos do seu cluster do AKS, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
