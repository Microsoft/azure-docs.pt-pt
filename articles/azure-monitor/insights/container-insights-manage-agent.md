---
title: Como gerir o Azure Monitor para agente de contentores | Documentos da Microsoft
description: Este artigo descreve a gerir as tarefas de manutenção mais comuns com o agente do Log Analytics em contentores utilizado pelo Azure Monitor para contentores.
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: e1d47be159d4721aed4b055a51acf675688b855e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65071806"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Como gerir o Azure Monitor para agente de contentores
Monitor do Azure para contentores utiliza uma versão em contentores do agente do Log Analytics para Linux. Após a implementação inicial, existem tarefas opcionais que poderá ter de efetuar durante seu ciclo de vida ou de rotina. Este artigo descreve a sobre como atualizar o agente e desativar a recolha de variáveis de ambiente de um determinado contêiner manualmente. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Como atualizar o Azure Monitor para agente de contentores
Monitor do Azure para contentores utiliza uma versão em contentores do agente do Log Analytics para Linux. Quando for lançada uma nova versão do agente, o agente é automaticamente atualizado nos seus clusters do Kubernetes geridos alojados no Azure Kubernetes Service (AKS).  

Se a atualização de agente falhar, este artigo descreve o processo de atualizar manualmente o agente. Para seguir as versões lançadas, consulte [anúncios de lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Atualizar o agente no cluster de Kubernetes monitorizado
O processo para atualizar o agente consiste em dois passos direta. A primeira etapa é desativar a monitorização com o Azure Monitor para contentores com a CLI do Azure.  Siga os passos descritos no [desativar a monitorização](container-insights-optout.md?#azure-cli) artigo. Com a CLI do Azure permite-nos remover o agente a partir de nós do cluster sem afetar a solução e os dados correspondentes, que são armazenados na área de trabalho. 

>[!NOTE]
>Enquanto estiver a efetuar esta atividade de manutenção, os nós do cluster não estão a reencaminhar os dados recolhidos e vistas de desempenho não apresentará dados entre a hora a remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga os passos descritos na [ativar a monitorização com a CLI do Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli), para concluir este processo.  

Depois da ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar as métricas de estado de funcionamento atualizado para o cluster. Para verificar se o agente atualizado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

O estado deve assemelhar-se o exemplo seguinte, onde o valor para *omi* e *omsagent* deve corresponder à versão mais recente especificada no [histórico de versões de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Como desativar a coleção de variáveis de ambiente num contêiner
Monitor do Azure para contentores recolhe variáveis de ambiente de contentores em execução num pod e apresenta-os no painel de propriedades do contentor selecionado na **contentores** vista. Pode controlar esse comportamento ao desativar a coleção para um contentor específico durante a implementação do AKS cluster, ou depois de definir a variável de ambiente *AZMON_COLLECT_ENV*. Esta funcionalidade está disponível a partir da versão de agente – ciprod11292018 e superior.  

Para desativar a coleção de variáveis de ambiente num contêiner de novo ou existente, defina a variável **AZMON_COLLECT_ENV** com um valor de **falso** no seu ficheiro de configuração de yaml de implementação de Kubernetes.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Execute o seguinte comando para aplicar a alteração para o seu contentor do AKS: `kubectl apply -f  <path to yaml file>`.

Para verificar a alteração da configuração demorou efeito, selecione um contentor no **contentores** ver no Azure Monitor para contentores e, no painel de propriedade, expanda **variáveis de ambiente**.  A secção deve mostrar apenas a variável que criou anteriormente - **AZMON_COLLECT_ENV = FALSE**. Para todos os outros contentores, a secção de variáveis de ambiente deve listar todas as variáveis de ambiente detetadas.   

Para voltar a ativar a deteção de variáveis de ambiente, aplicar o mesmo processo anteriormente e altere o valor de **False** ao **True**e, em seguida, volte a executar o `kubectl` comando para atualizar o contentor.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Passos Seguintes
Se ocorrerem problemas durante a atualização do agente, consulte a [guia de resolução de problemas](container-insights-troubleshoot.md) para o suporte.