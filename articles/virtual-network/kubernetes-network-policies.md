---
title: As políticas de rede de Kubernetes do Azure | Documentos da Microsoft
description: Saiba mais sobre sobre o Kubernetes políticas de rede para proteger o seu cluster de Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a5c367402bd1e61485095fd1d565a8582acc3a9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824906"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Kubernetes rede políticas descrição geral do Azure

Políticas de rede fornecem microssegmentação para pods, tal como grupos de segurança de rede (NSGs) fornecem microssegmentação para as VMs. A implementação de política de rede do Azure suporta a especificação de política de rede do Kubernetes padrão. Pode utilizar etiquetas para selecionar um grupo de pods e definir uma lista de regras de entrada e saída que especificam o tipo de tráfego que tem permissão de e para estes pods. Saiba mais sobre as políticas de rede do Kubernetes no [documentação do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Descrição geral das políticas de rede de Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Políticas de rede do Azure trabalham em conjunto com o Azure CNI que fornece integração de VNet para contentores. É suportada apenas em nós do Linux hoje. As implementações configurar regras de tabela de IP do Linux com base nas políticas definidas para impor a filtragem de tráfego.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planejamento de segurança para o seu cluster do Kubernetes
Durante a implementação de segurança para o seu cluster, utilize a rede grupos de segurança (NSGs) para filtrar o tráfego de Norte-Sul, ou seja, tráfego que entra e sai da sub-rede do cluster e utilizar políticas de rede do Kubernetes para o tráfego este-oeste, o que é. tráfego entre pods no seu cluster.

## <a name="using-azure-kubernetes-network-policies"></a>Através de políticas de rede de Kubernetes do Azure
Políticas de rede do Azure pode ser usadas das seguintes formas para fornecer microssegmentação de pods.

### <a name="acs-engine"></a>Motor de ACS
Motor de ACS é uma ferramenta que gera um modelo do Azure Resource Manager para a implementação de um cluster de Kubernetes no Azure. A configuração do cluster está especificada num ficheiro JSON transmitido à ferramenta ao gerar o modelo. Para saber mais sobre a lista completa das definições de cluster suportadas e suas descrições, consulte o motor do Microsoft Azure Container Service - a definição do Cluster.

Para ativar as políticas em clusters implementados através de motor de acs, especifique o valor da definição networkPolicy no arquivo de definição do cluster seja "azure".

#### <a name="example-configuration"></a>Configuração de exemplo

O abaixo JSON a configuração de exemplo cria uma nova rede virtual e uma sub-rede e implementa um cluster de Kubernetes no mesmo com CNI do Azure. Recomendamos que utilize "Bloco de notas" para editar o ficheiro JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      “kubernetesConfig”: {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Criar seu próprio cluster do Kubernetes no Azure
A implementação pode ser usada para fornecer as diretivas de rede para Pods em clusters do Kubernetes que implemente por conta própria, sem depender de ferramentas como o motor de ACS. Neste caso, primeiro instalar o plug-in de CNI e ativá-lo em todas as máquinas virtuais num cluster. Para obter instruções detalhadas, veja [Implementar o plug-in para um cluster do Kubernetes que implementar por conta própria](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Depois do cluster é implementado, execute o seguinte `kubectl` comando para transferir e aplicar a política de rede do Azure *daemonset* ao cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A solução também é parte do código-fonte aberto e o código está disponível na [repositório de redes de contentor do Azure](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [serviço Kubernetes do Azure](../aks/intro-kubernetes.md).
-  Saiba mais sobre [rede contentor](container-networking-overview.md).
- [Implementar o plug-in](deploy-container-networking.md) para clusters de Kubernetes ou de contentores do Docker.