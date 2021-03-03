---
title: Azure Arc permitiu a Arquitetura do Agente Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral arquitetônica de Azure Arc habilitado agentes Kubernetes
keywords: Kubernetes, Arc, Azure, contentores
ms.openlocfilehash: b4fb836cc7782f4026a28f4af0ca372c76486a31
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650537"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure Arc permitiu a Arquitetura do Agente Kubernetes

Por si só, [a Kubernetes](https://kubernetes.io/) pode implementar cargas de trabalho contentorizadas consistentemente em ambientes híbridos e multi-nuvens. Azure Arc permitiu que Kubernetes, no entanto, funciona como um plano de controlo centralizado e consistente que gere a política, governação e segurança em ambientes heterogéneos. Este artigo fornece:

* Uma visão geral arquitetónica de ligar um aglomerado ao Arco de Azure.
* O padrão de conectividade seguido por agentes.
* Uma descrição dos dados trocados entre o ambiente de cluster e o Azure.

## <a name="deploy-agents-to-your-cluster"></a>Desloque agentes para o seu cluster

A maioria dos centros de dados pré-directrizes de rede aplicam regras rígidas de rede que impedem a comunicação de entrada na firewall de fronteira da rede. O Azure Arc permitiu que a Kubernetes trabalhasse com estas restrições, não exigindo portas de entrada na firewall e apenas permitindo pontos finais seletivos de saída para a comunicação de saída. O Azure Arc permitiu que os agentes da Kubernetes iniciassem esta comunicação de saída. 

![Descrição geral da arquitetura](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Ligue um cluster ao Arco de Azure

1. Crie um cluster Kubernetes na sua escolha de infraestrutura (VMware vSphere, Amazon Web Services, Google Cloud Platform, etc.). 

    > [!NOTE]
    > Uma vez que a Azure Arc permitiu que a Kubernetes atualmente apenas suporta a anexação de clusters Kubernetes existentes ao Azure Arc, os clientes são obrigados a criar e gerir o ciclo de vida do cluster Kubernetes em si.  

1. Inicie o registo Azure Arc para o seu cluster utilizando o Azure CLI.
    * Azure CLI usa Helm para implantar o gráfico helm do agente no cluster.
    * Os nosdes de cluster iniciam uma comunicação de saída ao Registo de [Contentores](https://github.com/microsoft/containerregistry) da Microsoft e puxam as imagens necessárias para criar os seguintes agentes no `azure-arc` espaço de nome:

        | Agente | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc habilitado kubernetes atualmente suporta apenas [identidades atribuídas ao sistema](../../active-directory/managed-identities-azure-resources/overview.md). `clusteridentityoperator` inicia a primeira comunicação de saída. Esta primeira comunicação ressaltou o certificado de Identidade de Serviço Gerido (MSI) utilizado por outros agentes para comunicação com a Azure. |
        | `deployment.apps/config-agent` | Observa o cluster ligado para recursos de configuração de controlo de origem aplicados no cluster. Atualiza o estado de conformidade. |
        | `deployment.apps/controller-manager` | Um operador de operadores que orquestra interações entre componentes do Arco Azure. |    
        | `deployment.apps/metrics-agent` | Recolhe métricas de outros agentes da Arc para verificar o desempenho ideal. |
        | `deployment.apps/cluster-metadata-operator` | Recolhe metadados do cluster, incluindo a versão do cluster, a contagem de nós e a versão do agente do Azure Arc. |
        | `deployment.apps/resource-sync-agent` | Sincroniza os metadados de cluster acima mencionados para Azure. |
        | `deployment.apps/flux-logs-agent` | Recolhe registos dos operadores de fluxo implantados como parte da configuração do controlo de fontes. |
    
1. Uma vez que todas as cápsulas de agente kubernetes ativadas pelo Arco Azure estejam no `Running` estado, verifique se o seu cluster está ligado ao Arco Azure. Devia ver:
    * Um Arco Azure permitiu o recurso Kubernetes no [Azure Resource Manager](../../azure-resource-manager/management/overview.md). A Azure acompanha este recurso como uma projeção do cluster Kubernetes gerido pelo cliente, e não o cluster kubernetes em si.
    * Os metadados de cluster (como a versão Kubernetes, a versão do agente e o número de nós) aparecem no Azure Arc ativado recurso Kubernetes como metadados.

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
| Estado de configuração desejado: URL repositório git, parâmetros do operador de fluxo, chave privada, conteúdo de anfitriões conhecido, nome de utilizador HTTPS, token ou palavra-passe | Configuração | Agente puxa de Azure |
| Estado da instalação do operador de fluxo | Configuração | Agente empurra para Azure |
| Atribuição de política Azure que precisam de aplicação gatekeeper dentro do cluster | Azure Policy | Agente puxa de Azure |
| Auditoria e estatuto de conformidade das execuções das políticas em cluster | Azure Policy | Agente empurra para Azure |
| Métricas e registos de cargas de trabalho dos clientes | Azure Monitor | Agente empurra para log analytics recurso de espaço de trabalho no inquilino e subscrição do cliente |

## <a name="connectivity-status"></a>Estado da conectividade

| Estado | Descrição |
| ------ | ----------- |
| Ligação | Azure Arc ativou o recurso Kubernetes é criado em Azure Resource Manager, mas o serviço ainda não recebeu o batimento cardíaco do agente. |
| Ligada | Azure Arc ativou o serviço Kubernetes recebeu um batimento cardíaco de agente algures nos 15 minutos anteriores. |
| Offline | O recurso Azure Arc habilitado para a Kubernetes foi previamente ligado, mas o serviço não recebeu nenhum batimento cardíaco do agente durante 15 minutos. |
| Fora do prazo | O certificado MSI tem uma janela de validade de 90 dias após a sua emissão. Uma vez expirado este certificado, o recurso é considerado `Expired` e todas as funcionalidades tais como configuração, monitorização e política deixam de funcionar neste cluster. Mais informações sobre como lidar com o Azure Arc expirado habilitados a recursos kubernetes podem ser encontrados [no artigo da FAQ](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Compreender os modos de conectividade

| Modo conectividade | Descrição |
| ----------------- | ----------- |
| Totalmente conectado | Os agentes podem comunicar consistentemente com o Azure com pouco atraso na propagação das configurações do GitOps, na aplicação das políticas de Azure Policy e Gatekeeper, e na recolha de métricas e registos de carga de trabalho no Azure Monitor. |
| Semi-conectado | O certificado MSI retirado pelo `clusteridentityoperator` is valido até 90 dias antes do termo do certificado. Após a expiração, o Arco Azure permitiu que o recurso Kubernetes deixasse de funcionar. Para reativar todas as funcionalidades do Arco Azure no cluster, elimine e recrie o Arco Azure ativado recursos e agentes kubernetes. Durante os 90 dias, ligue o cluster pelo menos uma vez a cada 30 dias. |
| Desligado | Os aglomerados de Kubernetes em ambientes desligados incapazes de aceder a Azure não são atualmente suportados por Kubernetes ativados por Azure Arc. Se esta capacidade lhe interessar, submeta ou subjaga uma ideia no [fórum UserVoice da Azure Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Passos seguintes

* [Ligue um cluster ao Arco de Azure](./quickstart-connect-cluster.md)
* [Visão geral conceptual das configurações](./conceptual-configurations.md)