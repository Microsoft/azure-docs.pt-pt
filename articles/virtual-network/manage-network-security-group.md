---
title: Criar, alterar ou eliminar um grupo de segurança de rede Azure
titlesuffix: Azure Virtual Network
description: Aprenda a criar, alterar ou eliminar um grupo de segurança de rede.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: fa933b820d8677e4d080b54ce5e6a5d506ea38fc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245112"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Criar, alterar ou eliminar um grupo de segurança de rede

As regras de segurança nos grupos de segurança da rede permitem filtrar o tipo de tráfego de rede que pode fluir para dentro e para fora de subredes de rede virtuais e interfaces de rede. Se não estiver familiarizado com os grupos de segurança da rede, consulte a visão geral do grupo de [segurança da Rede](security-overview.md) para saber mais sobre eles e completar o tutorial de tráfego da rede [Filter](tutorial-filter-network-traffic.md) para obter alguma experiência com grupos de segurança da rede.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.come faça login na sua conta Azure.
- Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.28 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações apropriadas listadas nas [Permissões](#permissions).

## <a name="work-with-network-security-groups"></a>Trabalhar com grupos de segurança de rede

Pode criar, [visualizar todos, ver](#view-all-network-security-groups) [detalhes de,](#view-details-of-a-network-security-group) [alterar](#change-a-network-security-group)e [eliminar](#delete-a-network-security-group) um grupo de segurança da rede. Também pode [associar ou dissociar](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) um grupo de segurança de rede a partir de uma interface de rede ou subnet.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Há um limite para quantos grupos de segurança de rede você pode criar por localização e subscrição Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**.
2. **Selecione Networking**, em seguida, selecione **o grupo**de segurança da rede .
3. Introduza um **Nome** para o grupo de segurança da rede, selecione a sua **Subscrição,** crie um novo **grupo de Recursos,** ou selecione um grupo de recursos existente, selecione um **Local**e, em seguida, selecione **Criar**.

**Comandos**

- Azure CLI: [az network NSG criar](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Ver todos os grupos de segurança da rede

Na caixa de pesquisa no topo do portal, introduza *grupos*de segurança de rede . Quando os **grupos de segurança** da rede aparecerem nos resultados da pesquisa, selecione-os. Os grupos de segurança da rede que existem na sua subscrição estão listados.

**Comandos**

- Azure CLI: [lista de nsg da rede az](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Ver detalhes de um grupo de segurança de rede

1. Na caixa de pesquisa no topo do portal, introduza *grupos*de segurança de rede . Quando os **grupos de segurança** da rede aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança da rede na lista para o que pretende ver detalhes. Em **DEFINIÇÕES** pode ver as regras de **segurança de entrada** e as regras de segurança de **saída,** as **interfaces de rede** e **subnets** a que o grupo de segurança da rede está associado. Também pode ativar ou **desativar registos de diagnóstico** e visualizar regras de **segurança eficazes**. Para saber mais, consulte [registos de diagnóstico](virtual-network-nsg-manage-log.md) e [ver regras de segurança eficazes](diagnose-network-traffic-filter-problem.md).
3. Para saber mais sobre as definições comuns do Azure listadas, consulte os seguintes artigos:
    *   [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)
    *   [Controlo de acesso (IAM)](../role-based-access-control/overview.md)
    *   [Etiquetas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Fechaduras](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Roteiro de automação](../azure-resource-manager/templates/export-template-portal.md)

**Comandos**

- Azure CLI: [az network NSG show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Alterar um grupo de segurança de rede

1. Na caixa de pesquisa no topo do portal, introduza *grupos* de segurança de rede na caixa de pesquisa. Quando os **grupos de segurança** da rede aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança da rede que pretende alterar. As alterações mais comuns são [a adição](#create-a-security-rule) ou [remoção de](#delete-a-security-rule) regras de segurança e a associação ou dissociação de um grupo de segurança de rede de ou de uma interface de rede [ou subnet](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Comandos**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associar ou dissociar um grupo de segurança de rede de ou para uma interface de rede ou subnet

Para associar um grupo de segurança de rede a, ou dissociar um grupo de segurança de rede de uma interface de rede, consulte associate um grupo de [segurança de rede para, ou dissociar um grupo de segurança de rede de uma interface de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Para associar um grupo de segurança de rede ou dissociar um grupo de segurança de rede de uma subnet, consulte [as definições da sub-rede Change](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Eliminar um grupo de segurança de rede

Se um grupo de segurança de rede estiver associado a quaisquer subredes ou interfaces de rede, não pode ser eliminado. Dissociar um grupo de segurança de rede de todas as subredes e interfaces de rede antes de tentar eliminá-lo.

1. Na caixa de pesquisa no topo do portal, introduza *grupos* de segurança de rede na caixa de pesquisa. Quando os **grupos de segurança** da rede aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança da rede que pretende eliminar da lista.
3. **Selecione Eliminar**e, em seguida, selecione **Sim**.

**Comandos**

- Azure CLI: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Trabalhar com regras de segurança

Um grupo de segurança de rede contém zero ou mais regras de segurança. Pode criar, [ver todos, ver](#view-all-security-rules)detalhes [de,](#view-details-of-a-security-rule) [alterar](#change-a-security-rule)e [eliminar](#delete-a-security-rule) uma regra de segurança.

### <a name="create-a-security-rule"></a>Criar uma regra de segurança

Existe um limite para quantas regras por grupo de segurança de rede podem criar por localização e subscrição do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa no topo do portal, introduza *grupos* de segurança de rede na caixa de pesquisa. Quando os **grupos de segurança** da rede aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança da rede da lista a que pretende adicionar uma regra de segurança.
3. Selecione regras de **segurança de entrada** em **DEFINIÇÕES**. Várias regras existentes estão listadas. Algumas das regras que talvez não tenha adicionado. Quando um grupo de segurança de rede é criado, várias regras de segurança padrão são criadas nele. Para saber mais, consulte as regras de [segurança padrão](security-overview.md#default-security-rules).  Não pode eliminar regras de segurança por defeito, mas pode substituí-las com regras que têm uma prioridade maior.
4. <a name = "security-rule-settings"></a>Selecione **+ Adicionar**.  Selecione ou adicione valores para as seguintes definições e, em seguida, selecione **OK**:
    
    |Definição  |Valor  |Detalhes  |
    |---------|---------|---------|
    |Origem     | Selecione **Qualquer**, **grupo de segurança de aplicação,** **endereços IP**ou etiqueta de **serviço** para regras de segurança de entrada. Se estiver a criar uma regra de segurança de saída, as opções são as mesmas que as opções listadas para **destino**.       | Se selecionar o grupo de **segurança da Aplicação,** então selecione um ou mais grupos de segurança de aplicação existentes que existam na mesma região que a interface de rede. Saiba como criar um grupo de segurança de [aplicações.](#create-an-application-security-group) Se selecionar o grupo de **segurança da Aplicação** tanto para a **Fonte** como para o **Destino,** as interfaces de rede em ambos os grupos de segurança de aplicações devem estar na mesma rede virtual. Se selecionar **endereços IP,** especifique os **endereços IP/intervalos CIDR**. Pode especificar uma única lista de valores separados de valor ou víreu. Um exemplo de múltiplos valores é 10.0.0.0/16, 192.188.1.1.1. Existem limites para o número de valores que pode especificar. Consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para mais detalhes. Se selecionar **etiqueta de serviço,** então selecione uma etiqueta de serviço. Uma etiqueta de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as etiquetas de serviço disponíveis e sobre o que cada etiqueta representa, consulte etiquetas de [serviço.](security-overview.md#service-tags) Se o endereço IP que especifica for atribuído a uma máquina virtual Azure, certifique-se de que especifica o IP privado e não o endereço IP público atribuído à máquina virtual. As regras de segurança são processadas após o Azure traduzir o endereço IP público para um endereço IP privado para regras de segurança de entrada, e antes de Azure traduzir um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados em Azure, consulte os tipos de [endereços IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Intervalo de portas de origem     | Especifique uma única porta, como 80, uma gama de portos, tais como 1024-65535, ou uma lista separada de portas únicas e/ou faixas portuárias, tais como 80, 1024-65535. Introduza um asterisco para permitir o tráfego em qualquer porta. | Os portos e gamas especificam quais os portos que o tráfego é permitido ou negado pela regra. Existem limites para o número de portas que pode especificar. Consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para mais detalhes.  |
    |Destino     | Selecione **Qualquer**, Grupo de **segurança de aplicações,** **endereços IP**ou Rede **Virtual** para regras de segurança de saída. Se estiver a criar uma regra de segurança de entrada, as opções são as mesmas que as opções listadas para **a Fonte**.        | Se selecionar o grupo de **segurança da Aplicação,** deve selecionar um ou mais grupos de segurança de aplicações existentes que existam na mesma região que a interface de rede. Saiba como criar um grupo de segurança de [aplicações.](#create-an-application-security-group) Se selecionar o grupo de **segurança da Aplicação,** então selecione um grupo de segurança de aplicações existente que exista na mesma região que a interface de rede. Se selecionar **endereços IP,** especifique os **endereços IP de destino/intervalos CIDR**. Semelhante aos **endereços IP/gamas CIDR**de **Origem** e Origem, pode especificar um único, ou múltiplos endereços ou intervalos, e existem limites para o número que pode especificar. Selecionar **a rede Virtual**, que é uma etiqueta de serviço, significa que o tráfego é permitido a todos os endereços IP dentro do espaço de endereço da rede virtual. Se o endereço IP que especifica for atribuído a uma máquina virtual Azure, certifique-se de que especifica o IP privado e não o endereço IP público atribuído à máquina virtual. As regras de segurança são processadas após o Azure traduzir o endereço IP público para um endereço IP privado para regras de segurança de entrada, e antes de Azure traduzir um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados em Azure, consulte os tipos de [endereços IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Intervalos de portas de destino     | Especifique uma única lista de valores, ou com a vírem. | Semelhante às gamas de **portas Source,** pode especificar uma única, ou múltiplas portas e gamas, e existem limites para o número que pode especificar. |
    |Protocolo     | Selecione **Qualquer,** **TCP,** **UDP** ou **ICMP**.        |         |
    |Ação     | Selecione **Permitir** ou **Negar**.        |         |
    |Prioridade     | Introduza um valor entre 100-4096 que é único para todas as regras de segurança dentro do grupo de segurança da rede. |As regras são processadas por ordem prioritária. Quanto menor o número, maior a prioridade. Recomenda-se que deixe uma lacuna entre números prioritários ao criar regras, como 100, 200, 300. Deixar lacunas torna mais fácil adicionar regras no futuro que poderá ter de fazer mais ou menos do que as regras existentes.         |
    |Nome     | Um nome único para a regra dentro do grupo de segurança da rede.        |  O nome pode ser de até 80 caracteres. Deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado, e pode conter apenas letras, números, sublinhados, períodos ou hífenes.       |
    |Descrição     | Uma descrição opcional.        |         |

**Comandos**

- Azure CLI: [az rede nsg regra criar](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Ver todas as regras de segurança

Um grupo de segurança de rede contém zero ou múltiplas regras. Para saber mais sobre as informações listadas ao visualizar as regras, consulte a visão geral do [grupo de segurança da rede](security-overview.md).

1. Na caixa de pesquisa no topo do portal, introduza *grupos*de segurança de rede . Quando os **grupos de segurança** da rede aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança da rede da lista para a sua visualização de regras.
3. Selecione regras de **segurança de entrada** ou regras de segurança de **saída** ao abrigo **de DEFINIÇÕES**.

A lista contém quaisquer regras que tenha criado e as regras de [segurança padrão](security-overview.md#default-security-rules)do grupo de segurança da rede.

**Comandos**

- Azure CLI: lista de regras da [rede az NSG](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Ver detalhes de uma regra de segurança

1. Na caixa de pesquisa no topo do portal, introduza *grupos*de segurança de rede . Quando os **grupos de segurança** da rede aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança da rede para o quais pretende visualizar detalhes de uma regra de segurança.
3. Selecione regras de **segurança de entrada** ou regras de segurança de **saída** ao abrigo **de DEFINIÇÕES**.
4. Selecione a regra para a sua visualização. Para obter uma explicação detalhada de todas as definições, consulte [as definições](#security-rule-settings)da regra de segurança .

**Comandos**

- Azure CLI: [az network NSG rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Alterar uma regra de segurança

1. Complete os passos em [Ver detalhes de uma regra de segurança](#view-details-of-a-security-rule).
2. Altere as definições conforme desejado e, em seguida, selecione **Guardar**. Para obter uma explicação detalhada de todas as definições, consulte [as definições](#security-rule-settings)da regra de segurança .

**Comandos**

- Azure CLI: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Eliminar uma regra de segurança

1. Complete os passos em [Ver detalhes de uma regra de segurança](#view-details-of-a-security-rule).
2. **Selecione Eliminar**e, em seguida, selecione **Sim**.

**Comandos**

- Azure CLI: [regra nsg da rede az](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Trabalhar com grupos de segurança de aplicações

Um grupo de segurança de aplicações contém zero ou mais interfaces de rede. Para saber mais, consulte os grupos de segurança da [aplicação.](security-overview.md#application-security-groups) Todas as interfaces de rede num grupo de segurança de aplicações devem existir na mesma rede virtual. Para aprender a adicionar uma interface de rede a um grupo de segurança de aplicações, consulte Adicionar uma interface de rede a um grupo de segurança de [aplicações](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Criar um grupo de segurança de aplicações

1. Selecione **+ Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Na caixa **Pesquisar no Marketplace**, introduza *Grupo de segurança de aplicações*. Selecione **Grupo de segurança de aplicações** quando aparecer nos resultados da pesquisa, selecione novamente **Grupo de segurança de aplicações** em **Tudo** e, em seguida, selecione **Criar**.
3. Introduza, ou selecione as seguintes informações e, em seguida, selecione **Criar**:

    | Definição        | Valor                                                   |
    | ---            | ---                                                     |
    | Nome           | O nome tem de ser exclusivo dentro de um grupo de recursos.        |
    | Subscrição   | Selecione a sua subscrição.                               |
    | Grupo de recursos | Selecione um grupo de recursos existente, ou crie um novo. |
    | Localização       | Selecione uma localização                                       |

**Comandos**

- Azure CLI: [az rede asg criar](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Ver todos os grupos de segurança de aplicações

1. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure.
2. Introduza grupos de segurança de *aplicação* na caixa **de filtro de todos os serviços** e, em seguida, selecione grupos de segurança da **Aplicação** quando aparece nos resultados da pesquisa.

**Comandos**

- Azure CLI: [lista asg da rede az](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Ver detalhes de um grupo específico de segurança de aplicações

1. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure.
2. Introduza grupos de segurança de *aplicação* na caixa **de filtro de todos os serviços** e, em seguida, selecione grupos de segurança da **Aplicação** quando aparece nos resultados da pesquisa.
3. Selecione o grupo de segurança da aplicação que pretende ver os detalhes.

**Comandos**

- Azure CLI: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Alterar um grupo de segurança de aplicações

1. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure.
2. Introduza grupos de segurança de *aplicação* na caixa **de filtro de todos os serviços** e, em seguida, selecione grupos de segurança da **Aplicação** quando aparece nos resultados da pesquisa.
3. Selecione o grupo de segurança da aplicação para o que pretende alterar as definições. Pode adicionar ou remover etiquetas, ou atribuir ou remover permissões ao grupo de segurança da aplicação.

- Azure CLI: [az rede asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Sem cmdlet PowerShell.

### <a name="delete-an-application-security-group"></a>Eliminar um grupo de segurança de aplicações

Não é possível eliminar um grupo de segurança de aplicações se tiver quaisquer interfaces de rede no mesmo. Remova todas as interfaces de rede do grupo de segurança da aplicação alterando as definições de interface de rede ou eliminando as interfaces de rede. Para mais detalhes, consulte [Adicionar ou remover uma interface de rede de grupos de segurança de aplicações](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou eliminar uma interface de [rede](virtual-network-network-interface.md#delete-a-network-interface).

1. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure.
2. Introduza grupos de segurança de *aplicação* na caixa **de filtro de todos os serviços** e, em seguida, selecione grupos de segurança da **Aplicação** quando aparece nos resultados da pesquisa.
3. Selecione o grupo de segurança da aplicação que pretende eliminar.
4. **Selecione Eliminar**e, em seguida, selecione **Sim** para eliminar o grupo de segurança da aplicação.

**Comandos**

- Azure CLI: [az rede asg apagar](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Permissões

Para executar tarefas em grupos de segurança de rede, regras de segurança e grupos de segurança de aplicações, a sua conta deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as permissões adequadas listadas nas seguintes tabelas:

### <a name="network-security-group"></a>Grupo de segurança de rede

| Ação                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Obtenha o grupo de segurança da rede                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Criar ou atualizar grupo de segurança da rede                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Eliminar o grupo de segurança da rede                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associar um grupo de segurança de rede a uma interface de subnet ou rede 


### <a name="network-security-group-rule"></a>Regra do grupo de segurança da rede

| Ação                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Obter regra                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Criar ou atualizar regra                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Excluir regra                                                         |

### <a name="application-security-group"></a>Grupo de segurança de aplicações

| Ação                                                                     | Nome                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Junte-se a uma configuração IP para um grupo de segurança de aplicações|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Aderir a uma regra de segurança a um grupo de segurança de aplicações    |
| Microsoft.Network/applicationSecurityGroups/read                           | Obtenha um grupo de segurança de aplicações                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Criar ou atualizar um grupo de segurança de aplicações           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Eliminar um grupo de segurança de aplicações                     |

## <a name="next-steps"></a>Passos seguintes

- Criar um grupo de segurança de rede ou aplicação utilizando scripts de amostra [PowerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou utilizando [modelos](template-samples.md) de Gestor de Recursos Azure
- Criar e aplicar [a política azure](policy-samples.md) para redes virtuais
