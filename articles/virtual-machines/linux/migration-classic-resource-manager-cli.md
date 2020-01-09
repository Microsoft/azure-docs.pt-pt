---
title: Migrar VMs para o Gerenciador de recursos usando CLI do Azure
description: Este artigo percorre a migração de recursos com suporte da plataforma do clássico para o Azure Resource Manager usando CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 1ebb1ee5056d3b1e6e85bea43473de5918ddba5c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645179"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar recursos de IaaS do clássico para o Azure Resource Manager usando CLI do Azure
Estas etapas mostram como usar os comandos da CLI (interface de linha de comando) do Azure para migrar recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o modelo de implantação de Azure Resource Manager. O artigo requer a [CLI clássica do Azure](../../cli-install-nodejs.md). Como CLI do Azure é aplicável somente para recursos de Azure Resource Manager, ele não pode ser usado para essa migração.

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Se você tiver um problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. A plataforma tentará executar a ação novamente.
> 
> 

<br>
Aqui está um fluxograma para identificar a ordem em que as etapas precisam ser executadas durante um processo de migração

![Captura de ecrã que mostra os passos da migração](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Etapa 1: preparar para a migração
Aqui estão algumas práticas recomendadas que recomendamos ao avaliar a migração de recursos de IaaS do clássico para o Resource Manager:

* Leia a [lista de recursos ou configurações sem suporte](../windows/migration-classic-resource-manager-overview.md). Se você tiver máquinas virtuais que usam recursos ou configurações sem suporte, recomendamos que aguarde até que o suporte a recursos/configurações seja anunciado. Como alternativa, você pode remover esse recurso ou sair dessa configuração para habilitar a migração se atender às suas necessidades.
* Se você tiver scripts automatizados que implantam sua infraestrutura e seus aplicativos hoje, tente criar uma configuração de teste semelhante usando esses scripts para migração. Como alternativa, você pode configurar ambientes de exemplo usando o portal do Azure.

> [!IMPORTANT]
> Atualmente, os gateways de aplicativo não têm suporte para migração do clássico para o Gerenciador de recursos. Para migrar uma rede virtual clássica com um gateway de aplicativo, remova o gateway antes de executar uma operação de preparação para mover a rede. Depois de concluir a migração, reconecte o gateway em Azure Resource Manager. 
>
>Os gateways de ExpressRoute que se conectam a circuitos do ExpressRoute em outra assinatura não podem ser migrados automaticamente. Nesses casos, remova o gateway de ExpressRoute, migre a rede virtual e recrie o gateway. Consulte [migrar circuitos de ExpressRoute e redes virtuais associadas do modelo de implantação clássico para o Gerenciador de recursos](../../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Etapa 2: definir sua assinatura e registrar o provedor
Para cenários de migração, você precisa configurar seu ambiente para o clássico e o Resource Manager. [Instale CLI do Azure](../../cli-install-nodejs.md) e [Selecione sua assinatura](/cli/azure/authenticate-azure-cli).

Entre em sua conta.

    azure login

Selecione a assinatura do Azure usando o comando a seguir.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> O registro é uma etapa única, mas precisa ser feito uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro 
> 
> *BadRequest: a assinatura não está registrada para migração.* 
> 
> 

Registre-se com o provedor de recursos de migração usando o comando a seguir. Observe que, em alguns casos, esse comando atinge o tempo limite. No entanto, o registro será bem-sucedido.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos para que o registro seja concluído. Você pode verificar o status da aprovação usando o comando a seguir. Verifique se o Registrostate está `Registered` antes de continuar.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Agora, alterne a CLI para o modo de `asm`.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Etapa 3: Verifique se você tem uma máquina virtual de Azure Resource Manager suficiente vCPUs na região do Azure de sua implantação atual ou VNET
Para esta etapa, você precisará alternar para o modo de `arm`. Faça isso com o comando a seguir.

```
azure config mode arm
```

Você pode usar o seguinte comando da CLI para verificar o número atual de vCPUs que você tem em Azure Resource Manager. Para saber mais sobre cotas de vCPU, consulte [limites e o Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#limits-and-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Quando terminar de verificar esta etapa, você poderá voltar para o modo de `asm`.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Etapa 4: opção 1-migrar máquinas virtuais em um serviço de nuvem
Obtenha a lista de serviços de nuvem usando o comando a seguir e escolha o serviço de nuvem que você deseja migrar. Observe que, se as VMs no serviço de nuvem estiverem em uma rede virtual ou se tiverem funções Web/de trabalho, você receberá uma mensagem de erro.

    azure service list

Execute o comando a seguir para obter o nome da implantação para o serviço de nuvem da saída detalhada. Na maioria dos casos, o nome da implantação é o mesmo que o nome do serviço de nuvem.

    azure service show <serviceName> -vv

Primeiro, valide se você pode migrar o serviço de nuvem usando os seguintes comandos:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções para escolher.

Se você quiser migrar as VMs para uma rede virtual criada por plataforma, use o comando a seguir.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se você quiser migrar para uma rede virtual existente no modelo de implantação do Gerenciador de recursos, use o comando a seguir.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Depois que a operação de preparação for bem-sucedida, você poderá examinar a saída detalhada para obter o estado de migração das VMs e garantir que elas estejam no estado de `Prepared`.

    azure vm show <vmName> -vv

Verifique a configuração dos recursos preparados usando a CLI ou a portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o comando a seguir.

    azure service deployment abort-migration <serviceName> <deploymentName>

Se a configuração preparada estiver correta, você poderá avançar e confirmar os recursos usando o comando a seguir.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Etapa 4: opção 2-migrar máquinas virtuais em uma rede virtual
Escolha a rede virtual que você deseja migrar. Observe que, se a rede virtual contiver funções Web/de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Obtenha todas as redes virtuais na assinatura usando o comando a seguir.

    azure network vnet list

A saída terá uma aparência semelhante a esta:

![Captura de tela da linha de comando com o nome de rede virtual inteiro realçado.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, **virtualNetworkName** é o nome completo **"Group classicubuntu16 classicubuntu16"** .

Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Prepare a rede virtual de sua escolha para a migração usando o comando a seguir.

    azure network vnet prepare-migration <virtualNetworkName>

Verifique a configuração das máquinas virtuais preparadas usando a CLI ou a portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o comando a seguir.

    azure network vnet abort-migration <virtualNetworkName>

Se a configuração preparada estiver correta, você poderá avançar e confirmar os recursos usando o comando a seguir.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Etapa 5: migrar uma conta de armazenamento
Quando terminar de migrar as máquinas virtuais, recomendamos migrar a conta de armazenamento.

Preparar a conta de armazenamento para migração usando o comando a seguir

    azure storage account prepare-migration <storageAccountName>

Verifique a configuração da conta de armazenamento preparada usando a CLI ou a portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o comando a seguir.

    azure storage account abort-migration <storageAccountName>

Se a configuração preparada estiver correta, você poderá avançar e confirmar os recursos usando o comando a seguir.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da Comunidade para auxiliar na migração de recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Examine as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
