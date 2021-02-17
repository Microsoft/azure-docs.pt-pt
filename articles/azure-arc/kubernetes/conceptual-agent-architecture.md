---
title: Azure Arc permitiu a Arquitetura do Agente Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral arquitetônica de Azure Arc habilitado agentes Kubernetes
keywords: Kubernetes, Arc, Azure, contentores
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561675"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure Arc permitiu a Arquitetura do Agente Kubernetes

[Kubernetes](https://kubernetes.io/) pode ser usado para implantar cargas de trabalho contentorizadas em ambientes híbridos e multi-nuvens de forma consistente. Azure Arc permitiu que Kubernetes pudesse ser usado como um plano de controlo centralizado para gerir consistentemente a política, governação e segurança através destes ambientes heterogéneos. Este artigo fornece:

* Uma visão geral arquitetónica de ligar um aglomerado ao Arco de Azure.
* O padrão de conectividade seguido por agentes.
* Uma descrição dos dados trocados entre o ambiente do cluster e o Azure.

## <a name="deploy-agents-to-your-cluster"></a>Desloque agentes para o seu cluster

A maioria dos centros de dados pré-directrizes de rede aplicam regras rígidas de rede que impedem a comunicação de entrada na firewall utilizada na fronteira da rede. O Azure Arc permitiu que a Kubernetes trabalhasse com estas restrições apenas permitindo pontos finais seletivos de saída para a comunicação de saída e não exigindo portas de entrada na firewall. Azure Arc ativou os agentes kubernetes iniciando as ligações de saída.

![Descrição geral da arquitetura](./media/architectural-overview.png)

Ligue um cluster ao Arco Azure utilizando os seguintes passos:

1. Crie um cluster Kubernetes na sua escolha de infraestrutura (VMware vSphere, Amazon Web Services, Google Cloud Platform, etc.). 

    > [!NOTE]
    > Os clientes são obrigados a criar e gerir o ciclo de vida do cluster Kubernetes em si, uma vez que o Arco Azure permitiu que a Kubernetes atualmente apenas suporta a anexação de clusters Kubernetes existentes ao Arco Azure.  

1. Inicie o registo Azure Arc para o seu cluster utilizando o Azure CLI.
    * Azure CLI usa Helm para implantar o gráfico helm do agente no cluster.
    * Os nosdes de cluster iniciam uma comunicação de saída ao Registo de [Contentores](https://github.com/microsoft/containerregistry) da Microsoft e puxam as imagens necessárias para criar os seguintes agentes no `azure-arc` espaço de nome:

        | Agente | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc habilitado kubernetes atualmente suporta apenas [identidades atribuídas ao sistema](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). ClusteridentityOperator faz a primeira comunicação de saída necessária para obter o certificado de identidade de serviço gerido (MSI) usado por outros agentes para comunicação com a Azure. |
        | `deployment.apps/config-agent` | Observa o cluster conectado para recursos de configuração de controlo de origem aplicados no cluster e atualiza o estado de conformidade |
        | `deployment.apps/controller-manager` | Um operador de operadores que orquestra interações entre componentes do Arco Azure |    
        | `deployment.apps/metrics-agent` | Recolhe métricas de outros agentes da Arc para garantir que estes agentes estão a exibir um desempenho ideal |
        | `deployment.apps/cluster-metadata-operator` | Recolhe metadados de cluster - versão de cluster, contagem de nós e versão agente Azure Arc |
        | `deployment.apps/resource-sync-agent` | Sincroniza os metadados de cluster acima mencionados para Azure |
        | `deployment.apps/flux-logs-agent` | Recolhe registos dos operadores de fluxo implantados como parte da configuração do controlo de origem |
    
1. Uma vez que todo o Arco Azure ativou cápsulas de agente Kubernetes no `Running` estado, verifique se o seu cluster está ligado ao Arco Azure. Devia ver:
    * Um Arco Azure permitiu o recurso Kubernetes no [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Este recurso é rastreado em Azure como uma projeção do cluster Kubernetes gerido pelo cliente, e não o cluster kubernetes em si.
    * Os metadados de cluster, como a versão Kubernetes, a versão do agente e o número de nós, aparecem no Arco Azure ativado o recurso Kubernetes como metadados.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Troca de dados entre o ambiente de cluster e o Azure

| Tipo de dados | Scenario | Modo de comunicação |
| --------- | -------- | ------------------ |
| Versão do cluster do Kubernetes | Metadados de cluster | Agente empurra para Azure |
| Número de nós no cluster | Metadados de cluster | Agente empurra para Azure |
| Versão agente | Metadados de cluster | Agente empurra para Azure |
| Tipo de distribuição de Kubernetes | Metadados de cluster | Azure CLI empurra para Azure |
| Tipo de infraestrutura (AWS/GCP/vSphere/...) | Metadados de cluster | Azure CLI empurra para Azure |
| vCPU contagem de nóles no cluster | Faturação | Azure CLI empurra para Azure |
| Batimento cardíaco do agente | Estado de Funcionamento de Recursos | Agente empurra para Azure |
| Consumo de recursos (memória/CPU) por agentes | Diagnóstico e apoio | Agente empurra para Azure |
| Registos de todos os contentores de agentes | Diagnóstico e apoio | Agente empurra para Azure |
| Disponibilidade de upgrade de agente | Atualização de agente | Agente puxa de Azure |
| Estado desejado de Configuração - GIT repo URL, parâmetros do operador de fluxo, chave privada, conteúdo conhecido dos anfitriões, nome de utilizador HTTPS, token/password | Configuração | Agente puxa de Azure |
| Estado da instalação do operador de fluxo | Configuração | Agente empurra para Azure |
| Atribuição de política Azure que precisam de aplicação gatekeeper dentro do cluster | Azure Policy | Agente puxa de Azure |
| Auditoria e estatuto de conformidade das execuções das políticas em cluster | Azure Policy | Agente empurra para Azure |
| Métricas e registos de cargas de trabalho dos clientes | Azure Monitor | Agente empurra para log analytics recurso de espaço de trabalho no inquilino e subscrição do cliente |

## <a name="connectivity-status"></a>Estado da conectividade

| Estado | Descrição |
| ------ | ----------- |
| Ligação | O Azure Arc permitiu o recurso Kubernetes criado no Azure Resource Manager, mas o serviço ainda não recebeu batimentos cardíacos do agente. |
| Ligada | Azure Arc ativou o serviço Kubernetes recebeu um batimento cardíaco de agente algures nos 15 minutos anteriores. |
| Offline | O recurso Azure Arc habilitado para a Kubernetes foi previamente ligado, mas o serviço não recebeu nenhum batimento cardíaco do agente durante 15 minutos. |
| Fora do prazo | O certificado de identidade de serviço gerido (MSI) tem uma janela de validade de 90 dias após a sua emissão. Uma vez expirado este certificado, o recurso é considerado `Expired` e todas as funcionalidades como configuração, monitorização e política deixam de funcionar neste cluster. Mais informações sobre como lidar com o Azure Arc expirado habilitados recursos Kubernetes podem ser encontrados [aqui](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources) |

## <a name="understand-connectivity-modes"></a>Compreender os modos de conectividade

| Modo conectividade | Descrição |
| ----------------- | ----------- |
| Totalmente conectado | Os agentes são sempre capazes de contactar o Azure. A experiência é ideal neste caso, uma vez que há pouco atraso na propagação de configurações (para GitOps), aplicação de políticas (em Azure Policy e Gatekeeper) e recolha de métricas e registos de cargas de trabalho (em Azure Monitor) |
| Semi-conectado | O certificado MSI retirado pelo `clusteridentityoperator` é válido por 90 dias antes do certificado expirar. Uma vez expirado o certificado, o Arco Azure permitiu que o recurso Kubernetes deixasse de funcionar. Eliminar e recriar o Arco Azure permitiu que os recursos e agentes da Kubernetes conseguissem que todas as funcionalidades do Arco funcionassem no cluster. Durante os 90 dias, recomenda-se aos utilizadores que liguem o cluster pelo menos uma vez a cada 30 dias. |
| Desligado | Os aglomerados de Kubernetes em ambientes desligados sem qualquer acesso ao Azure não são atualmente suportados por Kubernetes habilitados pelo Azure Arc. Se esta capacidade lhe interessar, submeta ou subjaga uma ideia no [fórum UserVoice da Azure Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Passos seguintes

* [Ligue um cluster ao Arco de Azure](./connect-cluster.md)
* [Visão geral conceptual das configurações](./conceptual-configurations.md)