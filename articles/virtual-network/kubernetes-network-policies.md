---
title: Políticas de rede Azure Kubernetes Microsoft Docs
description: Saiba mais sobre as políticas de rede kubernetes para garantir o seu cluster Kubernetes.
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
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73159690"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Descrição geral das políticas de rede do Kubernetes do Azure

As Políticas de Rede fornecem micro-segmentação para cápsulas, tal como os Grupos de Segurança de Rede (NSGs) fornecem micro-segmentação para VMs. A implementação da Política de Rede Azure suporta a especificação padrão da Política de Rede Kubernetes. Você pode usar etiquetas para selecionar um grupo de cápsulas e definir uma lista de regras de entrada e saída que especificam o tipo de tráfego que é permitido de e para estas cápsulas. Saiba mais sobre as políticas de rede Kubernetes na [documentação kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Visão geral das políticas de rede de Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

As políticas de rede Azure funcionam em conjunto com o Azure CNI que proporciona integração VNet para contentores. É apoiado apenas nos nós Linux hoje. As implementações configuram as regras do Quadro IP do Linux com base nas políticas definidas para impor a filtragem do tráfego.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Segurança de planeamento para o seu cluster Kubernetes
Ao implementar a segurança do seu cluster, utilize grupos de segurança de rede (NSGs) para filtrar North-South tráfego, ou seja, o tráfego entrando e saindo da sua sub-rede de cluster, e use as políticas de rede Kubernetes para East-West tráfego, isto é, tráfego entre cápsulas no seu cluster.

## <a name="using-azure-kubernetes-network-policies"></a>Usando as políticas de rede Azure Kubernetes
As políticas de rede Azure podem ser usadas das seguintes formas para fornecer micro-segmentação para cápsulas.

### <a name="acs-engine"></a>Motor ACS
ACS-Engine é uma ferramenta que gera um modelo de Gestor de Recursos Azure para a implementação de um cluster Kubernetes em Azure. A configuração do cluster está especificada num ficheiro JSON transmitido à ferramenta ao gerar o modelo. Para saber mais sobre a lista completa de definições de cluster suportadas e as respetivas descrições, veja Motor do Microsoft Azure Container Service – Definição do Cluster.

Para permitir políticas em clusters implantados utilizando o motor acs, especifique o valor da definição de redePolicy no ficheiro de definição de cluster como "azul".

#### <a name="example-configuration"></a>Configuração de exemplo

A configuração de exemplo JSON abaixo cria uma nova rede virtual e sub-rede, e implementa um cluster Kubernetes nele com Azure CNI. Recomendamos que utilize o "Notepad" para editar o ficheiro JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
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
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Criando o seu próprio cluster Kubernetes em Azure
A implementação pode ser usada para fornecer Políticas de Rede para Pods em clusters Kubernetes que você próprio implanta, sem depender de ferramentas como o ACS-Engine. Neste caso, instale primeiro o plug-in CNI e ative-o em todas as máquinas virtuais de um cluster. Para obter instruções detalhadas, veja [Implementar o plug-in para um cluster do Kubernetes que implementar por conta própria](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Uma vez implantado o cluster, executar o `kubectl` seguinte comando para descarregar e aplicar o *daemonset de* política de rede Azure no cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A solução também é de código aberto e o código está disponível no [repositório de rede de contentores Azure.](https://github.com/Azure/azure-container-networking/tree/master/npm)



## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Serviço Azure Kubernetes](../aks/intro-kubernetes.md).
-  Saiba mais sobre [a rede de contentores.](container-networking-overview.md)
- [Insiú-lo](deploy-container-networking.md) para os aglomerados kubernetes ou para os recipientes Docker.
