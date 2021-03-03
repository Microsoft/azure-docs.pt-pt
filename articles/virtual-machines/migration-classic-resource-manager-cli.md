---
title: Migrar VMs para Gestor de Recursos usando O Azure CLI
description: Este artigo percorre a migração de recursos suportados pela plataforma do clássico para o Azure Resource Manager utilizando o Azure CLI.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 671b27f927c91397d2aacd98cb7b500d8197d1c5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669337"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrate IaaS resources from classic to Azure Resource Manager by using Azure CLI (Migrar recursos IaaS de clássica para Azure Resource Manager com a CLI do Azure)

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs da IaaS estão a usar [o Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VM clássicos foram depreciados e serão totalmente retirados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](classic-vm-deprecation.md#how-does-this-affect-me)

Estes passos mostram-lhe como utilizar comandos de interface de linha de comandos Azure (CLI) para migrar a infraestrutura como um serviço (IaaS) recursos do modelo de implementação clássico para o modelo de implementação do Azure Resource Manager. O artigo requer o CLI clássico do [Azure.](/cli/azure/install-classic-cli) Uma vez que o Azure CLI só é aplicável aos recursos do Gestor de Recursos Azure, não pode ser utilizado para esta migração.

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Se tiver outro problema que não seja uma funcionalidade não suportada ou um erro de configuração, recomendamos que relemisse a preparação, abortar ou cometer o funcionamento. A plataforma tentará a ação novamente.
> 
> 

<br>
Aqui está um fluxograma para identificar a ordem em que as etapas precisam ser executadas durante um processo de migração

![Captura de ecrã que mostra os passos da migração](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Passo 1: Preparar para a migração
Aqui estão algumas boas práticas que recomendamos à medida que avalia os recursos iaaS migratórios do clássico para o Gestor de Recursos:

* Leia a [lista de configurações ou funcionalidades não suportadas](migration-classic-resource-manager-overview.md). Se tiver máquinas virtuais que utilizem configurações ou funcionalidades não suportadas, recomendamos que aguarde que o suporte de funcionalidade/configuração seja anunciado. Em alternativa, pode remover essa funcionalidade ou sair dessa configuração para ativar a migração se se adequar às suas necessidades.
* Se tiver scripts automatizados que implementem a sua infraestrutura e aplicações hoje em dia, tente criar uma configuração de teste semelhante usando esses scripts para migração. Em alternativa, pode configurar ambientes de amostra utilizando o portal Azure.

> [!IMPORTANT]
> Os Gateways de Aplicação não são atualmente suportados para a migração do clássico para o Gestor de Recursos. Para migrar uma rede virtual clássica com um gateway de aplicação, remova o gateway antes de executar uma operação Prepare para mover a rede. Depois de concluir a migração, reconecte a porta de entrada no Azure Resource Manager. 
>
>Os gateways ExpressRoute que se ligam aos circuitos ExpressRoute noutra subscrição não podem ser migrados automaticamente. Nesses casos, remova o gateway ExpressRoute, migrar a rede virtual e recriar o gateway. Consulte os [circuitos Migrate ExpressRoute e as redes virtuais associadas do clássico ao modelo de implementação do Gestor de Recursos](../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Passo 2: Descreva a sua subscrição e registe o fornecedor
Para cenários de migração, você precisa configurar o seu ambiente tanto para o clássico como para o Gestor de Recursos. [Instale o Azure CLI](/cli/azure/install-classic-cli) e [selecione a sua subscrição](/cli/azure/authenticate-azure-cli).

Inscreva-se na sua conta.

```azurecli
azure login
```

Selecione a subscrição Azure utilizando o seguinte comando.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> O registo é um passo único, mas tem de ser feito uma vez antes de tentar migrar. Sem registar verá a seguinte mensagem de erro 
> 
> *BadRequest : A subscrição não está registada para migração.* 
> 
> 

Registe-se junto do fornecedor de recursos de migração utilizando o seguinte comando. Note que em alguns casos, este comando acaba. No entanto, o registo será bem sucedido.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Por favor, espere cinco minutos para a inscrição terminar. Pode verificar o estado da aprovação utilizando o seguinte comando. Certifique-se de que o Estado de Registo está `Registered` antes de prosseguir.

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Agora mude o CLI para o `asm` modo.

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passo 3: Certifique-se de que tem vCPUs virtual do Gestor de Recursos Azure na região Azure da sua atual implantação ou VNET
Para este passo terá de mudar para `arm` o modo. Faça isto com o seguinte comando.

```azurecli
azure config mode arm
```

Pode utilizar o seguinte comando CLI para verificar o número atual de vCPUs que tem no Azure Resource Manager. Para saber mais sobre as quotas vCPU, consulte [Limits e o Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Uma vez terminado a verificação deste passo, pode voltar ao `asm` modo.

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Passo 4: Opção 1 - Migrar máquinas virtuais num serviço de nuvem
Obtenha a lista de serviços em nuvem usando o seguinte comando e, em seguida, escolha o serviço de nuvem que deseja migrar. Note que se os VMs no serviço de nuvem estiverem numa rede virtual ou se tiverem funções web/trabalhador, receberá uma mensagem de erro.

```azurecli
azure service list
```

Executar o seguinte comando para obter o nome de implementação do serviço de nuvem a partir da saída verbose. Na maioria dos casos, o nome de implantação é o mesmo que o nome de serviço em nuvem.

```azurecli
azure service show <serviceName> -vv
```

Em primeiro lugar, valide se conseguir migrar o serviço de nuvem utilizando os seguintes comandos:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Tem duas opções para escolher.

Se pretender migrar os VMs para uma rede virtual criada pela plataforma, utilize o seguinte comando.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Se pretender migrar para uma rede virtual existente no modelo de implementação do Gestor de Recursos, utilize o seguinte comando.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

Depois de a operação de preparação ter sido bem sucedida, pode olhar através da produção verbosa para obter o estado de migração dos VMs e garantir que estão no `Prepared` estado.

```azurecli
azure vm show <vmName> -vv
```

Verifique a configuração dos recursos preparados utilizando o CLI ou o portal Azure. Se não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Passo 4: Opção 2 - Migrar máquinas virtuais numa rede virtual
Escolha a rede virtual que pretende migrar. Note que se a rede virtual contiver funções web/trabalhador ou VMs com configurações não suportadas, receberá uma mensagem de erro de validação.

Obtenha todas as redes virtuais na subscrição utilizando o seguinte comando.

```azurecli
azure network vnet list
```

A saída será semelhante à seguinte:

![Screenshot da linha de comando com todo o nome de rede virtual realçado.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, o **virtualNetworkName** é o nome completo **"Group classicubuntu16 classicubuntu16"**.

Em primeiro lugar, valide se conseguir migrar a rede virtual utilizando o seguinte comando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Prepare a rede virtual da sua escolha para a migração utilizando o seguinte comando.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

Verifique a configuração das máquinas virtuais preparadas utilizando o CLI ou o portal Azure. Se não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>Passo 5: Migrar uma conta de armazenamento
Uma vez terminada a migração das máquinas virtuais, recomendamos que emigres a conta de armazenamento.

Prepare a conta de armazenamento para a migração utilizando o seguinte comando

```azurecli
azure storage account prepare-migration <storageAccountName>
```

Verifique a configuração da conta de armazenamento preparada utilizando o CLI ou o portal Azure. Se não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da migração suportada pela plataforma de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Use o PowerShell para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Ferramentas comunitárias para ajudar na migração de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)
* [Reveja as perguntas mais frequentes sobre a migração dos recursos da IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md)
