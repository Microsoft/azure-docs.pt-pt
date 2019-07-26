---
title: Políticas de rede kubernetes do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de rede kubernetes para proteger o cluster kubernetes.
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
ms.openlocfilehash: ff6fd45e0a68a3e93e4c62eb31a566a6dffa2344
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494955"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Visão geral das políticas de rede kubernetes do Azure

As políticas de rede fornecem a microsegmentação para pods, assim como NSGs (grupos de segurança de rede) fornecem o micro segmentação para VMs. A implementação da política de rede do Azure dá suporte à especificação de política de rede kubernetes padrão. Você pode usar rótulos para selecionar um grupo de pods e definir uma lista de regras de entrada e saída que especificam o tipo de tráfego que é permitido para e desse pods. Saiba mais sobre as políticas de rede kubernetes na [documentação do kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Visão geral das políticas de rede do kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

As políticas de rede do Azure funcionam em conjunto com o Azure CNI que fornece integração VNet para contêineres. Atualmente, há suporte apenas em nós do Linux. As implementações configuram as regras de tabela IP do Linux com base nas políticas definidas para impor a filtragem de tráfego.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planejando a segurança para o cluster kubernetes
Ao implementar a segurança para o cluster, use NSGs (grupos de segurança de rede) para filtrar o tráfego Norte-Sul, ou seja, o tráfego que entra e sai da sub-rede do cluster e usa políticas de rede kubernetes para o tráfego leste-oeste, ou seja, o tráfego entre pods seu cluster.

## <a name="using-azure-kubernetes-network-policies"></a>Usando as políticas de rede kubernetes do Azure
As políticas de rede do Azure podem ser usadas das seguintes maneiras para fornecer a microsegmentação para pods.

### <a name="acs-engine"></a>ACS-mecanismo
O ACS-Engine é uma ferramenta que gera um modelo de Azure Resource Manager para a implantação de um cluster kubernetes no Azure. A configuração do cluster está especificada num ficheiro JSON transmitido à ferramenta ao gerar o modelo. Para saber mais sobre a lista completa de configurações de cluster com suporte e suas descrições, consulte Microsoft Azure mecanismo de serviço de contêiner-definição de cluster.

Para habilitar políticas em clusters implantados usando o ACS-Engine, especifique o valor da configuração networkPolicy no arquivo de definição de cluster como "Azure".

#### <a name="example-configuration"></a>Configuração de exemplo

A configuração de exemplo JSON abaixo cria uma nova rede virtual e sub-rede e implanta um cluster kubernetes nela com o Azure CNI. Recomendamos que você use o "bloco de notas" para editar o arquivo JSON. 
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
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Criando seu próprio cluster kubernetes no Azure
A implementação pode ser usada para fornecer políticas de rede para pods em clusters kubernetes que você mesmo implanta, sem depender de ferramentas como o ACS-Engine. Nesse caso, primeiro instale o plug-in CNI e habilite-o em todas as máquinas virtuais em um cluster. Para obter instruções detalhadas, veja [Implementar o plug-in para um cluster do Kubernetes que implementar por conta própria](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Depois que o cluster for implantado `kubectl` , execute o comando a seguir para baixar e aplicar o *daemonset* de política de rede do Azure ao cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A solução também é de código-fonte aberto e o código está disponível no [repositório de rede de contêiner do Azure](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o [serviço kubernetes do Azure](../aks/intro-kubernetes.md).
-  Saiba mais sobre a [rede](container-networking-overview.md)de contêineres.
- [Implante o plug-in](deploy-container-networking.md) para clusters kubernetes ou contêineres do Docker.
