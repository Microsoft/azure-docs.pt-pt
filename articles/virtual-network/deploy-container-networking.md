---
title: Implementar o funcionamento em rede de contentores da rede virtual do Azure | Microsoft Docs
description: Saiba como implementar o plug-in da interface de rede de contentores (CNI) da Rede Virtual do Azure para clusters do Kubernetes implementados por si, implementados através do ACS-Engine e para contentores do Docker.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 657c23ad410d7aade17b3153f02ba0138edf4250
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104102"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Implementar o plug-in da interface de rede de contentores da Rede Virtual do Azure

O plug-in da interface de rede de contentores (CNI) da Rede Virtual do Azure é instalado numa máquina virtual do Azure e fornece capacidades de rede virtual aos contentores de Pods do Kubernetes e do Docker. Para saber mais sobre o plug-in, veja [Ativar contentores para utilizar as capacidades da Rede Virtual do Azure](container-networking-overview.md). Além disso, o plug-in pode ser utilizado com o Azure Kubernetes Service (AKS) ao selecionar a opção [Redes Avançadas](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), que coloca automaticamente os contentores do AKS numa rede virtual.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Implementar um plug-in para o cluster do Kubernetes do ACS-Engine

O ACS-Engine implementa um cluster do Kubernetes através de um modelo do Azure Resource Manager. A configuração do cluster está especificada num ficheiro JSON transmitido à ferramenta ao gerar o modelo. Para saber mais sobre a lista completa de definições de cluster suportadas e as respetivas descrições, veja [Motor do Microsoft Azure Container Service – Definição do Cluster](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md). O plug-in é o funcionamento em rede predefinido para os clusters criados com o ACS-Engine. As seguintes definições de configuração de rede são importantes quando configurar o plug-in:

  | Definição                              | Descrição                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | O endereço IP alocado ao nó Principal. É uma definição obrigatória.                                     |
  | clusterSubnet em kubernetesConfig | CIDR da sub-rede da rede virtual em que o cluster está implementado e a partir do qual os IP endereços são alocados a Pods   |
  | vnetSubnetId em masterProfile     | Especifica o ID de recurso do Azure Resource Manager da sub-rede onde o cluster será implementado                    |
  | vnetCidr                             | CIDR da rede virtual onde o cluster está implementado                                                             |
  | Max-Pods em kubeletConfig         | Número máximo de Pods em cada máquina virtual do agente. Para o plug-in, a predefinição é 30. Pode especificar até 250  |

### <a name="example-configuration"></a>Configuração de exemplo

O exemplo de json que se segue destina-se a um cluster com as seguintes propriedades:
-   1 nó Principal e 2 nós de Agente 
-   É implementado numa sub-rede com o nome *KubeClusterSubnet* (10.0.0.0/20), com os nós Principal e Agente residentes.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Implementar um plug-in para um cluster do Kubernetes

Conclua os seguintes passos para instalar o plug-in em todas as máquinas virtuais do Azure num cluster do Kubernetes:

1. [Transfira e instale o plug-in](#download-and-install-the-plug-in).
2. Pré-aloque um conjunto de endereços IP de rede virtual em todas as máquinas virtuais a partir do qual os endereços IP serão alocados a Pods. Todas as máquinas virtuais do Azure incluem um endereço IP privado primário de rede virtual em cada interface de rede. O conjunto de endereços IP para Pods é adicionado como endereços secundários (*ipconfigs*) na interface de rede da máquina virtual, através de uma das seguintes opções:

   - **CLI**: [atribuir vários endereços IP com a CLI do Azure](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [atribuir vários endereços IP com o PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
   - **Portal**: [atribuir vários endereços IP com o portal do Azure](virtual-network-multiple-ip-addresses-portal.md)
   - **Modelo Azure Resource Manager**: [atribuir vários endereços IP através de modelos](virtual-network-multiple-ip-addresses-template.md)

   Certifique-se de que adiciona endereços IP suficientes para todos os Pods que pretende colocar na máquina virtual.

3. Selecione o plug-in para o fornecimento de redes do cluster ao transmitir a opção da linha de comandos `–network-plugin=cni` Kubelet durante a criação do cluster. Por predefinição, o Kubernetes procura o plug-in e o ficheiro de configuração nos diretórios onde já estão instalados.
4. Se quiser que os Pods acedam à internet, adicione a seguinte regra *iptables* nas suas máquinas virtuais do Linux ao tráfego de Internet de origem NAT. No exemplo seguinte, o intervalo IP especificado é 10.0.0.0/8.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   O tráfego NAT das regras não é destinado aos intervalos IP especificados. A suposição é que todo o tráfego fora dos intervalos anteriores é tráfego de Internet. Pode optar por especificar os intervalos IP da rede virtual da máquina virtual, das redes virtuais em modo de peering e das redes no local.

   As máquinas virtuais do Windows extraem automaticamente o tráfego do NAT de origem com destino fora da sub-rede à qual pertence a máquina virtual. Não é possível especificar intervalos IP personalizados.

Depois de concluir os passos anteriores, são atribuídos automaticamente endereços IP privados da rede virtual aos Pods colocados nas máquinas virtuais do Agente do Kubernetes.

## <a name="deploy-plug-in-for-docker-containers"></a>Implementar plug-in para contentores do Docker

1. [Transfira e instale o plug-in](#download-and-install-the-plug-in).
2. Crie contentores do Docker com o seguinte comando:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

Os contentores começam a receber automaticamente os endereços IP do conjunto alocado. Se quiser criar um balanceador de carga de tráfego para os contentores do Docker, estes têm de ser colocados atrás de um balanceador de carga de software e tem de configurar uma sonda do balanceador de carga, da mesma forma que cria uma política e sondas para uma máquina virtual.

### <a name="cni-network-configuration-file"></a>Ficheiro de configuração de rede CNI

O ficheiro de configuração de rede CNI é descrito no formato JSON. Por predefinição, está presente em `/etc/cni/net.d` para Linux e em `c:\cni\netconf` para Windows. O ficheiro especifica a configuração do plug-in e é diferente para Windows e Linux. O json que se segue é um ficheiro de configuração do Linux de exemplo, seguido de uma explicação para algumas das definições-chave. Não precisa de fazer quaisquer alterações ao ficheiro:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Explicação das definições

- **cniVersion**: Os plug-ins CNI de rede Virtual do Azure suportam versões 0.3.0 e 0.3.1 do [CNI spec](https://github.com/containernetworking/cni/blob/master/SPEC.md).
- **name**: Nome da rede. Esta propriedade pode ser definida para qualquer valor exclusivo.
- **type**: Nome da rede Plug-in. Defina como *vnet do azure*.
- **Modo**: Modo operacional. Este campo é opcional. O único modo suportado é "bridge". Para obter mais informações, consulte [modos operacionais](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md).
- **ponte**: Nome da ponte que será utilizado para ligar contentores a uma rede virtual. Este campo é opcional. Se for omitido, o plug-in escolhe automaticamente um nome exclusivo, com base no índice de interface principal.
- **tipo de ipam**: Nome do IPAM Plug-in. Sempre definido como *azure-vnet-ipam*.

## <a name="download-and-install-the-plug-in"></a>Transferir e instalar o plug-in

Transfira o plug-in a partir do [GitHub](https://github.com/Azure/azure-container-networking/releases). Transfira a versão mais recente para a plataforma que está a utilizar:

- **Linux**: [azure-vnet-cni-linux-amd64-\<version no.\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [azure-vnet-cni-windows-amd64-\<version no.\>.zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Copie o script de instalação para [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) ou [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) para o seu computador. Guarde o script no diretório `scripts` no seu computador e dê o nome `install-cni-plugin.sh` ao ficheiro para Linux, ou `install-cni-plugin.ps1` para Windows. Para instalar o plug-in, execute o script adequado para a sua plataforma e especifique a versão do plug-in que está a utilizar. Por exemplo, pode especificar *v1.0.12-rc3*:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

O script instala o plug-in em `/opt/cni/bin` para Linux e em `c:\cni\bin` para Windows. O plug-in instalado inclui um ficheiro de configuração de rede simples que funciona após a instalação. Não tem de ser atualizado. Para saber mais sobre as definições no ficheiro, veja [Ficheiro de configuração de rede CNI](#cni-network-configuration-file).