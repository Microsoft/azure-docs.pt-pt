---
title: Migrar VMs para o Resource Manager com CLI do Azure | Documentos da Microsoft
description: Este artigo explica a migração suportada por plataforma de recursos da implementação clássica para Azure Resource Manager com CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 34dad39e3784dd0bc73e3be108d6b31d4f479a1e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543275"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar recursos de IaaS do clássico para o Azure Resource Manager com CLI do Azure
Estes passos mostram como utilizar comandos de interface de linha de comandos (CLI) do Azure para migrar a infraestrutura como um recursos de serviço (IaaS) do modelo de implementação clássica para o modelo de implementação Azure Resource Manager. O artigo requer a [CLI clássica do Azure](../../cli-install-nodejs.md). Uma vez que a CLI do Azure só é aplicável para recursos do Azure Resource Manager, não pode ser utilizado para essa migração.

> [!NOTE]
> Todas as operações descritas aqui são idempotent. Se tiver um problema que não seja um recurso não suportado ou um erro de configuração, recomendamos que repita a preparação, aborto ou consolidação a operação. A plataforma será, em seguida, repita a ação.
> 
> 

<br>
Eis um fluxograma para identificar a ordem em que passos têm de ser executado durante um processo de migração

![Captura de ecrã que mostra os passos da migração](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Passo 1: Preparar para migração
Seguem-se algumas melhores práticas que recomendamos durante a avaliação migrar recursos de IaaS do clássico para Resource Manager:

* Leia os [lista de configurações não suportadas ou funcionalidades](../windows/migration-classic-resource-manager-overview.md). Se tiver máquinas virtuais que utilizem as configurações não suportadas ou funcionalidades, recomendamos que aguarde o suporte de configuração/funcionalidades serão anunciados. Em alternativa, pode remover essa funcionalidade ou mover para fora de que a configuração para permitir a migração se ele atenda às suas necessidades.
* Se tem automatizadas scripts que implemente a sua infraestrutura e aplicações hoje mesmo, tente criar uma configuração de teste semelhante com esses scripts para a migração. Em alternativa, pode configurar ambientes de exemplo com o portal do Azure.

> [!IMPORTANT]
> Gateways de aplicação não são atualmente suportados para migração da implementação clássica para Resource Manager. Para migrar uma rede virtual clássica com um gateway de aplicação, remova o gateway antes de executar uma operação de preparação para mover a rede. Depois de concluir a migração, volte a ligar o gateway no Azure Resource Manager. 
>
>Ligar a circuitos do ExpressRoute noutra subscrição gateways do ExpressRoute não podem ser migradas automaticamente. Nesses casos, remover o gateway do ExpressRoute, migrar a rede virtual e recriar o gateway. Veja [Migrate ExpressRoute circuits e associadas a redes virtuais do clássico para o modelo de implementação do Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Passo 2: Definir a subscrição e registar o fornecedor
Para cenários de migração, terá de configurar o ambiente para clássicas e do Resource Manager. [Instalar a CLI do Azure](../../cli-install-nodejs.md) e [selecione a sua subscrição](/cli/azure/authenticate-azure-cli).

Início de sessão para a sua conta.

    azure login

Selecione a subscrição do Azure com o comando seguinte.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> O registo é um passo de tempo, mas ele precisa ser feito uma vez antes de tentar migrar. Sem Registro verá a seguinte mensagem de erro 
> 
> *BadRequest: Subscrição não está registada para migração.* 
> 
> 

Registre-se com o fornecedor de recursos de migração utilizando o seguinte comando. Tenha em atenção de que em alguns casos, este comando exceder o tempo limite. No entanto, o registo será concluída com êxito.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos a concluir do registo. Pode verificar o estado da aprovação com o comando seguinte. Certifique-se de que é RegistrationState `Registered` antes de continuar.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Agora alternar CLI para o `asm` modo.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passo 3: Certifique-se de que tem suficiente vCPUs de Máquina Virtual do Azure Resource Manager na região do Azure da sua implementação atual ou a VNET
Para este passo terá de mudar para `arm` modo. Fazer isso com o seguinte comando.

```
azure config mode arm
```

Pode utilizar o seguinte comando da CLI para verificar o número atual de vCPUs que tiver no Azure Resource Manager. Para saber mais sobre as quotas de vCPU, consulte [limites e o Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Quando tiver terminado a verificar este passo, pode voltar a mudar para `asm` modo.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Passo 4: Opção 1 - migrar máquinas virtuais num serviço cloud
Obter a lista de serviços cloud, utilizando o comando seguinte e, em seguida, selecione o serviço de nuvem que pretende migrar. Tenha em atenção que se as VMs no serviço cloud estão numa rede virtual ou se tiverem funções da web/de trabalho, obterá uma mensagem de erro.

    azure service list

Execute o seguinte comando para obter o nome de implementação para o serviço em nuvem a partir da saída verbosa. Na maioria dos casos, o nome da implementação é o mesmo que o nome do serviço cloud.

    azure service show <serviceName> -vv

Em primeiro lugar, valide se é possível migrar o serviço em nuvem com os comandos seguintes:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Prepare as máquinas virtuais no serviço cloud para a migração. Tem duas opções à sua escolha.

Se pretender migrar as VMs a uma rede virtual criada por plataforma, utilize o seguinte comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se pretender migrar para uma rede virtual existente no modelo de implementação do Resource Manager, utilize o seguinte comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Depois da operação de preparação é efetuada com êxito, pode examinar a saída detalhada para obter o estado de migração das VMs e certifique-se de que estão no `Prepared` estado.

    azure vm show <vmName> -vv

Verifique a configuração para os recursos preparados, com a CLI ou o portal do Azure. Se não está preparado para migração e pretender voltar atrás para o estado antigo, utilize o seguinte comando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Se a configuração preparada parece bom, pode seguir em frente e consolide os recursos com o seguinte comando.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Passo 4: Opção 2 - migrar máquinas virtuais numa rede virtual
Escolha a rede virtual que pretende migrar. Tenha em atenção que, se a rede virtual contém funções da web/de trabalho ou VMs com as configurações não suportadas, receberá uma mensagem de erro de validação.

Obtenha todas as redes virtuais na subscrição, utilizando o seguinte comando.

    azure network vnet list

A saída será algo parecido com isto:

![Captura de ecrã da linha de comandos com o nome de toda a rede virtual realçado.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, o **virtualNetworkName** é o nome completo **"Grupo classicubuntu16 classicubuntu16"**.

Em primeiro lugar, valide se é possível migrar a rede virtual com o seguinte comando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Prepare-se a rede virtual à sua escolha para a migração usando o seguinte comando.

    azure network vnet prepare-migration <virtualNetworkName>

Verifique a configuração para as máquinas virtuais preparadas, com a CLI ou o portal do Azure. Se não está preparado para migração e pretender voltar atrás para o estado antigo, utilize o seguinte comando.

    azure network vnet abort-migration <virtualNetworkName>

Se a configuração preparada parece bom, pode seguir em frente e consolide os recursos com o seguinte comando.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Passo 5: Migrar uma conta de armazenamento
Quando tiver terminado a migrar as máquinas virtuais, recomendamos que migre a conta de armazenamento.

Preparar a conta de armazenamento para migração usando o seguinte comando

    azure storage account prepare-migration <storageAccountName>

Verifique a configuração para a conta de armazenamento preparado, com a CLI ou o portal do Azure. Se não está preparado para migração e pretender voltar atrás para o estado antigo, utilize o seguinte comando.

    azure storage account abort-migration <storageAccountName>

Se a configuração preparada parece bom, pode seguir em frente e consolide os recursos com o seguinte comando.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral da migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utilizar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da Comunidade para auxiliar na migração de recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
