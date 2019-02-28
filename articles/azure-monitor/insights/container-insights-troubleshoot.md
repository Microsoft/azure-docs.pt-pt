---
title: Como resolver problemas relacionados com o Azure Monitor para contentores | Documentos da Microsoft
description: Este artigo descreve como pode resolver problemas e resolver problemas com o Azure Monitor para contentores.
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
ms.date: 11/30/2018
ms.author: magoedte
ms.openlocfilehash: abf833cc054bfac0581506f75259e357f0ab1b38
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985755"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Resolução de problemas do Azure Monitor para contentores

Quando configurar a monitorização do seu cluster do Azure Kubernetes Service (AKS) com o Azure Monitor para contentores, poderá ocorrer um problema que impede a recolha de dados ou comunicar o estado. Este artigo fornece detalhes sobre alguns problemas comuns e passos de resolução de problemas.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Monitor do Azure para contentores está ativado mas não comunicam quaisquer informações
Se o Monitor do Azure para contentores com êxito é ativado e configurado, mas não é possível ver informações de estado ou não são devolvidos resultados de uma consulta de registo, diagnosticar o problema ao seguir estes passos: 

1. Verifique o estado do agente ao executar o comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve assemelhar-se o seguinte, que indica que foi implementado devidamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verifique o estado de implementação com a versão do agente *06072018* ou posteriormente utilizando o comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve assemelhar-se ao seguinte exemplo, o que indica que foi implementado devidamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Verifique o estado de pod para verificar se ele está em execução com o comando: `kubectl get pods --namespace=kube-system`

    A saída deve assemelhar-se o exemplo a seguir com o estado *em execução* para o omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Verifique os registos do agente. Quando o agente em contentores é implementado, ele executa uma verificação rápida ao executar comandos OMI e apresenta a versão do agente e fornecedor. 

5. Para verificar se o agente foram carregadas com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

    O estado deve assemelhar-se o exemplo seguinte:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Mensagens de erro

A tabela abaixo resume erros conhecidos que pode encontrar ao utilizar o Azure Monitor para contentores.

| Mensagens de erro  | Ação |  
| ---- | --- |  
| Mensagem de erro `No data for selected filters`  | Pode demorar algum tempo a estabelecer o fluxo de dados de monitorização para os clusters recém-criado. Aguarde, pelo menos, 10 a 15 minutos para os dados são apresentados para o seu cluster. |   
| Mensagem de erro `Error retrieving data` | Enquanto o cluster do Azure Kubenetes Service é a configuração para o estado de funcionamento e monitorização de desempenho, é estabelecida uma ligação entre o cluster e a área de trabalho do Log Analytics do Azure. Uma área de trabalho do Log Analytics é utilizada para armazenar todos os dados de monitorização para o seu cluster. Este erro pode ocorrer quando a sua área de trabalho do Log Analytics foi eliminada ou perdida. Verifique se a sua área de trabalho está disponível ao rever [gerir o acesso](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#view-workspace-details). Se a área de trabalho está em falta, terá de re-carregar para o cluster com o Azure Monitor para contentores. Para re-carregar, precisará [desativar](container-insights-optout.md) de monitorização para o cluster e [ativar](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) Monitor do Azure para contentores novamente. |  
| `Error retrieving data` Depois de adicionar o Azure Monitor para contentores através da cli do az aks | Quando utilizar a integração `az aks cli`, muito raramente, do Azure Monitor para contentores pode não ser corretamente carregadas. Verifique se a solução está carregada. Para tal, aceda à sua área de trabalho do Log Analytics e ver se a solução está disponível, selecionando **soluções** partir do painel no lado esquerdo. Para resolver este problema, terá de voltar a implementar a solução ao seguir as instruções [como implementar o Azure Monitor para contentores](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json) |  

Para ajudar a diagnosticar o problema, nós fornecemos um script de resolução de problemas disponível [aqui](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Passos Seguintes
Com a monitorização ativada para capturar métricas de estado de funcionamento para os nós de cluster do AKS e os pods, estas métricas de estado de funcionamento estão disponíveis no portal do Azure. Para saber como utilizar o Azure Monitor para contentores, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).