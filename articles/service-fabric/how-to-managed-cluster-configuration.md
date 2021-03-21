---
title: Configure o cluster gerido do seu Tecido de Serviço (pré-visualização)
description: Saiba como configurar o seu cluster gerido pelo Service Fabric para atualizações automáticas de SO, regras NSG e muito mais.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732637"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Opções de configuração geridas por cluster de tecido de serviço (pré-visualização)

Além de selecionar o [cluster gerido pelo Service Fabric SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) ao criar o seu cluster, existem várias outras formas de o configurar. Na pré-visualização atual, pode:

* Configure [opções de networking](how-to-managed-cluster-networking.md) para o seu cluster
* Adicione uma [extensão de conjunto de escala de máquina virtual](how-to-managed-cluster-vmss-extension.md) a um tipo de nó
* Configurar [identidade gerida](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) nos seus tipos de nó
* Ativar [upgrades automáticos de SO](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) para os seus nos seus nosmos
* Ativar [a encriptação do sistema operativo e do disco de dados](how-to-enable-managed-cluster-disk-encryption.md) nos seus nos seus

## <a name="enable-automatic-os-image-upgrades"></a>Ativar upgrades automáticos de imagem de SO

Pode optar por ativar atualizações automáticas de imagens de SO para as máquinas virtuais que executam os seus nós de cluster geridos. Embora os recursos de escala de máquina virtual sejam geridos em seu nome com clusters geridos por Service Fabric, a escolha é ativar upgrades automáticos de imagem de SO para os seus nós de cluster. Tal como acontece com os agrupamentos [clássicos de Tecido](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) de Serviço, os nós de cluster geridos não são atualizados por padrão, de modo a evitar perturbações não intencionais no seu cluster.

Para permitir atualizações automáticas de SO:

* Utilize a `2021-01-01-preview` versão (ou posterior) do *Microsoft.ServiceFabric/gerenciou os aglomerados* e os recursos *de Microsoft.ServiceFabric/managedclusters/nodetypes*
* Definir a propriedade do cluster `enableAutoOSUpgrade` para *verdadeiro*
* Desaprote a propriedade de recursos dos nodeTypes de cluster `vmImageVersion` para o *mais recente*

Por exemplo:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Uma vez ativado, o Service Fabric começará a consultar e rastrear as versões de imagem do SO no cluster gerido. Se estiver disponível uma nova versão SO, os tipos de nó de cluster (conjuntos de escala de máquina virtual) serão atualizados, um de cada vez. As atualizações de tempo de execução do tecido de serviço só são executadas depois de confirmar que não estão em curso atualizações de imagem do nó de cluster.

Se uma atualização falhar, o Service Fabric tentará novamente após 24 horas, para um máximo de três recauchutagens. Semelhantes às atualizações clássicas (não geridas) do Tecido de Serviço, aplicações ou nós pouco saudáveis podem bloquear a atualização da imagem do SO.

Para obter mais informações sobre atualizações de imagens, consulte [atualizações automáticas de imagens de SO com conjuntos de escala de máquina virtual Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos clusters geridos de tecido de serviço](overview-managed-cluster.md)

[Modelos de cluster do Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)
