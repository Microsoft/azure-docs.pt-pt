---
title: Como gerenciar o Azure Monitor para agente de contêineres | Microsoft Docs
description: Este artigo descreve o gerenciamento das tarefas de manutenção mais comuns com o agente de Log Analytics em contêineres usado pelo Azure Monitor para contêineres.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/06/2018
ms.openlocfilehash: bfedd7989e71bcb8cf58cef7ad7122749350ae26
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554048"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Como gerenciar o Azure Monitor para agente de contêineres
Azure Monitor para contêineres usa uma versão em contêiner do agente de Log Analytics para Linux. Após a implantação inicial, há tarefas rotineiras ou opcionais que talvez você precise executar durante seu ciclo de vida. Este artigo detalha como atualizar manualmente o agente e desabilitar a coleta de variáveis ambientais de um contêiner específico. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Como atualizar o Azure Monitor para agente de contêineres
Azure Monitor para contêineres usa uma versão em contêiner do agente de Log Analytics para Linux. Quando uma nova versão do agente é liberada, o agente é atualizado automaticamente em seus clusters kubernetes gerenciados hospedados no AKS (serviço kubernetes do Azure).  

Se a atualização do agente falhar, este artigo descreverá o processo para atualizar manualmente o agente. Para seguir as versões lançadas, consulte [comunicados de versão do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Atualizando o agente no cluster kubernetes monitorado
O processo para atualizar o agente consiste em duas etapas diretas. A primeira etapa é desabilitar o monitoramento com Azure Monitor para contêineres usando CLI do Azure.  Siga as etapas descritas no artigo [desabilitar monitoramento](container-insights-optout.md?#azure-cli) . O uso de CLI do Azure nos permite remover o agente dos nós no cluster sem afetar a solução e os dados correspondentes que são armazenados no espaço de trabalho. 

>[!NOTE]
>Enquanto você estiver executando essa atividade de manutenção, os nós no cluster não estão encaminhando dados coletados e as exibições de desempenho não mostrarão dados entre o momento em que você remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga as etapas descritas em [habilitar monitoramento usando CLI do Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli)para concluir este processo.  

Depois de habilitar novamente o monitoramento, pode levar cerca de 15 minutos para que você possa exibir as métricas de integridade atualizadas para o cluster. Para verificar se o agente foi atualizado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

O status deve ser semelhante ao exemplo a seguir, em que o valor de *OMI* e *omsagent* deve corresponder à versão mais recente especificada no [histórico de lançamento do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Como desabilitar a coleção de variáveis de ambiente em um contêiner
Azure Monitor para contêineres coleta variáveis ambientais dos contêineres em execução em um pod e as apresenta no painel de propriedades do contêiner selecionado na exibição **contêineres** . Você pode controlar esse comportamento desabilitando a coleta de um contêiner específico durante a implantação do cluster AKS ou depois definindo a variável de ambiente *AZMON_COLLECT_ENV*. Esse recurso está disponível na versão do agente – ciprod11292018 e superior.  

Para desabilitar a coleta de variáveis de ambiente em um contêiner novo ou existente, defina a variável **AZMON_COLLECT_ENV** com um valor de **false** em seu arquivo de configuração YAML de implantação do kubernetes.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Execute o seguinte comando para aplicar a alteração ao seu contêiner AKS: `kubectl apply -f  <path to yaml file>`.

Para verificar se as alterações de configuração entraram em vigor, selecione um contêiner na exibição **contêineres** em Azure monitor para contêineres e, no painel de propriedades, expanda **variáveis de ambiente**.  A seção deve mostrar apenas a variável criada anteriormente- **AZMON_COLLECT_ENV = false**. Para todos os outros contêineres, a seção variáveis de ambiente deve listar todas as variáveis de ambiente descobertas.   

Para reabilitar a descoberta das variáveis ambientais, aplique o mesmo processo anteriormente e altere o valor de **false** para **true**e, em seguida, execute novamente o comando `kubectl` para atualizar o contêiner.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Passos seguintes
Se você tiver problemas ao atualizar o agente, examine o [Guia de solução de problemas](container-insights-troubleshoot.md) para obter suporte.