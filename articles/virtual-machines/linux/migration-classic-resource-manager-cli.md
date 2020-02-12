---
title: Migrar VMs para Gestor de Recursos usando o Azure CLI
description: Este artigo percorre a migração de recursos suportados pela plataforma do clássico para o Gestor de Recursos Azure, utilizando o Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 43be6947b464a18cc29edc5a578fd8879b70bacf
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153184"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar recursos IaaS do clássico para o Gestor de Recursos Azure usando o Azure CLI
Estes passos mostram-lhe como utilizar comandos de interface de linha de comando Azure (CLI) para migrar a infraestrutura como um serviço (IaaS) recursos do modelo de implantação clássico para o modelo de implantação do Gestor de Recursos Azure. O artigo requer o [clássico CLI azure.](../../cli-install-nodejs.md) Uma vez que o Azure CLI só é aplicável aos recursos do Gestor de Recursos Do Azure, não pode ser utilizado para esta migração.

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Se tiver um problema que não seja uma funcionalidade não suportada ou um erro de configuração, recomendamos que tente novamente a preparação, abortar ou comprometer o funcionamento. A plataforma tentará novamente a ação.
> 
> 

<br>
Aqui está um fluxograma para identificar a ordem em que os passos precisam ser executados durante um processo de migração

![Captura de ecrã que mostra os passos da migração](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Passo 1: Preparar-se para a migração
Aqui estão algumas boas práticas que recomendamos ao avaliar os recursos iaaS migratórios do clássico para o Gestor de Recursos:

* Leia a [lista de configurações ou funcionalidades não suportadas](../windows/migration-classic-resource-manager-overview.md). Se tiver máquinas virtuais que utilizem configurações ou funcionalidades não suportadas, recomendamos que aguarde o anúncio do suporte de funcionalidade/configuração. Em alternativa, pode remover essa funcionalidade ou sair dessa configuração para permitir a migração se se adequar às suas necessidades.
* Se tem scripts automatizados que implementam a sua infraestrutura e aplicações hoje em dia, tente criar uma configuração de teste semelhante utilizando esses scripts para migração. Em alternativa, pode configurar ambientes de amostra utilizando o portal Azure.

> [!IMPORTANT]
> Os Gateways de aplicação não são atualmente suportados para a migração do clássico para o Gestor de Recursos. Para migrar uma rede virtual clássica com um gateway de aplicação, remova o portal antes de executar uma operação Prepare para mover a rede. Depois de completar a migração, religue a porta de entrada no Azure Resource Manager. 
>
>Os gateways ExpressRoute que ligam aos circuitos ExpressRoute numa outra subscrição não podem ser migrados automaticamente. Nesses casos, remova o gateway ExpressRoute, migra a rede virtual e recrie o portal. Consulte [os circuitos Migrate ExpressRoute e redes virtuais associadas do clássico ao modelo](../../expressroute/expressroute-migration-classic-resource-manager.md) de implementação do Gestor de Recursos para obter mais informações.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Passo 2: Desmarcar a sua subscrição e registar o fornecedor
Para cenários de migração, você precisa configurar o seu ambiente tanto para o clássico como para o Gestor de Recursos. [Instale o Azure CLI](../../cli-install-nodejs.md) e [selecione a sua subscrição](/cli/azure/authenticate-azure-cli).

Inscreva-se na sua conta.

    azure login

Selecione a subscrição Azure utilizando o seguinte comando.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> O registo é um passo único, mas tem de ser feito uma vez antes de tentar a migração. Sem se registar verá a seguinte mensagem de erro 
> 
> *BadRequest : A subscrição não está registada para migração.* 
> 
> 

Registe-se no fornecedor de recursos migratórios utilizando o seguinte comando. Note que em alguns casos, este comando passa. No entanto, a inscrição será bem sucedida.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Por favor, espere cinco minutos para a inscrição terminar. Pode verificar o estado da aprovação utilizando o seguinte comando. Certifique-se de que o Estado de Registo está `Registered` antes de prosseguir.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Agora mude o CLI para o modo `asm`.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passo 3: Certifique-se de que tem vCPUs de Máquina Virtual do Gestor de Recursos Azure suficiente seleções na região Azure da sua atual implantação ou VNET
Para este passo, terá de mudar para `arm` modo. Faça isto com o seguinte comando.

```
azure config mode arm
```

Pode utilizar o seguinte comando CLI para verificar o número atual de vCPUs que tem no Gestor de Recursos Azure. Para saber mais sobre as quotas vCPU, consulte [Limits e o Gestor de Recursos Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Uma vez feito o check-in, pode voltar a `asm` modo.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Passo 4: Opção 1 - Migrar máquinas virtuais num serviço de nuvem
Obtenha a lista de serviços na nuvem utilizando o seguinte comando e, em seguida, escolha o serviço de nuvem que pretende migrar. Note que se os VMs do serviço de nuvem estiverem numa rede virtual ou se tiverem funções web/trabalhador, receberá uma mensagem de erro.

    azure service list

Execute o seguinte comando para obter o nome de implantação para o serviço de nuvem a partir da saída verbosa. Na maioria dos casos, o nome de implantação é o mesmo que o nome do serviço na nuvem.

    azure service show <serviceName> -vv

Primeiro, valide se conseguir migrar o serviço de nuvem utilizando os seguintes comandos:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Tem duas opções para escolher.

Se quiser migrar os VMs para uma rede virtual criada pela plataforma, utilize o seguinte comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se pretender migrar para uma rede virtual existente no modelo de implementação do Gestor de Recursos, utilize o seguinte comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Depois de a operação de preparação ser bem sucedida, pode olhar através da produção verbosa para obter o estado de migração dos VMs e garantir que estão no estado `Prepared`.

    azure vm show <vmName> -vv

Verifique a configuração dos recursos preparados utilizando o CLI ou o portal Azure. Se não está pronto para a migração e quer voltar para o antigo estado, use o seguinte comando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Passo 4: Opção 2 - Migrar máquinas virtuais numa rede virtual
Escolha a rede virtual que quer migrar. Note que se a rede virtual contiver funções web/trabalhador ou VMs com configurações não suportadas, receberá uma mensagem de erro de validação.

Obtenha todas as redes virtuais na subscrição utilizando o seguinte comando.

    azure network vnet list

A saída será mais ou menos assim:

![Screenshot da linha de comando com todo o nome da rede virtual realçado.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, o **virtualNetworkName** é o nome completo **"Grupo classicubuntu16"** .

Primeiro, valide se conseguir migrar a rede virtual utilizando o seguinte comando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Prepare a rede virtual à sua escolha para a migração utilizando o seguinte comando.

    azure network vnet prepare-migration <virtualNetworkName>

Verifique a configuração das máquinas virtuais preparadas utilizando o CLI ou o portal Azure. Se não está pronto para a migração e quer voltar para o antigo estado, use o seguinte comando.

    azure network vnet abort-migration <virtualNetworkName>

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Passo 5: Migrar uma conta de armazenamento
Uma vez que você terminar de migrar as máquinas virtuais, recomendamos que emigra a conta de armazenamento.

Preparar a conta de armazenamento para migração utilizando o seguinte comando

    azure storage account prepare-migration <storageAccountName>

Verifique a configuração da conta de armazenamento preparada utilizando o CLI ou o portal Azure. Se não está pronto para a migração e quer voltar para o antigo estado, use o seguinte comando.

    azure storage account abort-migration <storageAccountName>

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Use a PowerShell para migrar os recursos iaaS do clássico para o Gestor de Recursos Azure](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas comunitárias para ajudar na migração de recursos iaaS do clássico para o Gestor de Recursos Azure](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
