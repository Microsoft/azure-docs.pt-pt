---
title: Políticas de rede Azure Kubernetes Microsoft Docs
description: Conheça as políticas de rede kubernetes para garantir o seu cluster Kubernetes.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73159690"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Descrição geral das políticas de rede do Kubernetes do Azure

As Políticas de Rede fornecem microsegmentação para cápsulas como os Grupos de Segurança da Rede (NSGs) fornecem microsegmentação para VMs. A implementação da Política de Rede Azure apoia a especificação padrão da Política de Rede Kubernetes. Pode utilizar etiquetas para selecionar um grupo de cápsulas e definir uma lista de regras de entrada e saída que especificam o tipo de tráfego que é permitido de e para estas cápsulas. Saiba mais sobre as políticas de rede Kubernetes na [documentação kubernetes.](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

![Visão geral das políticas de rede kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

As políticas de rede Azure funcionam em conjunto com o Azure CNI que fornece integração VNet para contentores. É apoiado apenas nos nódosos Linux hoje. As implementações configuram as regras da tabela IP linux com base nas políticas definidas para impor a filtragem do tráfego.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planejando segurança para o seu cluster Kubernetes
Ao implementar a segurança do seu cluster, utilize grupos de segurança de rede (NSGs) para filtrar o tráfego Norte-Sul, isto é, entrada de tráfego e saída da sua subnet cluster, e use as políticas de rede Kubernetes para o tráfego Leste-Oeste, isto é, tráfego entre cápsulas em seu aglomerado.

## <a name="using-azure-kubernetes-network-policies"></a>Utilização de políticas de rede Azure Kubernetes
As Políticas de Rede Azure podem ser utilizadas de formas que sejam utilizadas para fornecer microsegmentação para cápsulas.

### <a name="acs-engine"></a>Motor ACS
O ACS-Engine é uma ferramenta que gera um modelo de Gestor de Recursos Azure para a implantação de um cluster Kubernetes em Azure. A configuração do cluster está especificada num ficheiro JSON transmitido à ferramenta ao gerar o modelo. Para saber mais sobre a lista completa de definições de cluster suportadas e as respetivas descrições, veja Motor do Microsoft Azure Container Service – Definição do Cluster.

Para permitir que as políticas em clusters implantados utilizando o motor ACS, especifique o valor da definição de política de rede no ficheiro de definição de cluster "azul".

#### <a name="example-configuration"></a>Configuração de exemplo

A configuração de exemplo jSON abaixo cria uma nova rede virtual e subnet, e implanta um cluster Kubernetes nele com Azure CNI. Recomendamos que utilize o "Bloco de Notas" para editar o ficheiro JSON. 
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
A implementação pode ser usada para fornecer Políticas de Rede para Pods em clusters Kubernetes que você se implanta, sem depender de ferramentas como o ACS-Engine. Neste caso, instala primeiro o plug-in CNI e ativa-o em todas as máquinas virtuais de um cluster. Para obter instruções detalhadas, veja [Implementar o plug-in para um cluster do Kubernetes que implementar por conta própria](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Uma vez que o cluster `kubectl` é implantado executar o seguinte comando para descarregar e aplicar a política de rede Azure *daemonset* para o cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A solução também é fonte aberta e o código está disponível no repositório de rede de [contentores Azure.](https://github.com/Azure/azure-container-networking/tree/master/npm)



## <a name="next-steps"></a>Passos seguintes
- Conheça o [Serviço Azure Kubernetes.](../aks/intro-kubernetes.md)
-  Saiba mais sobre [a rede de contentores.](container-networking-overview.md)
- [Desloque o plug-in](deploy-container-networking.md) para os clusters Kubernetes ou para os contentores Docker.
