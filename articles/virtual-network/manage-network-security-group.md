---
title: Criar, alterar ou eliminar um grupo de segurança de rede do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar, alterar ou eliminar um grupo de segurança de rede.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 5eb5a24d6126e9609d1c653948c2db6b0a4feb55
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821939"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Criar, alterar ou eliminar um grupo de segurança de rede

Regras de segurança em grupos de segurança de rede permitem-lhe filtrar o tipo de tráfego de rede que pode fluir dentro e fora de sub-redes da rede virtual e interfaces de rede. Se não estiver familiarizado com os grupos de segurança de rede, consulte [descrição geral de grupo de segurança de rede](security-overview.md) para saber mais sobre eles e concluir o [filtrar o tráfego de rede](tutorial-filter-network-traffic.md) tutorial para ter alguma experiência na rede grupos de segurança.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a CLI do Azure versão 2.0.28 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

Deve ser atribuída a conta iniciar sessão no, ou ligar ao Azure com para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [permissões ](#permissions).

## <a name="work-with-network-security-groups"></a>Trabalhar com grupos de segurança de rede

Pode criar, [ver todas](#view-all-network-security-groups), [ver os detalhes das](#view-details-of-a-network-security-group), [alterar](#change-a-network-security-group), e [eliminar](#delete-a-network-security-group) um grupo de segurança de rede. Também pode [associar ou desassociar](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) um grupo de segurança de rede de uma interface de rede ou sub-rede.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Existe um limite para quantas que pode criar por subscrição e localização do Azure de grupos de segurança de rede. Para obter mais detalhes, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No canto superior esquerdo do portal, selecione **+ criar um recurso**.
2. Selecione **Networking**, em seguida, selecione **grupo de segurança de rede**.
3. Introduza um **Name** para o grupo de segurança de rede, selecione seu **subscrição**, crie um novo **grupo de recursos**, ou selecione um grupo de recursos existente, selecione um **Localização**e, em seguida, selecione **criar**.

**Comandos**

- CLI do Azure: [criar az rede nsg](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Ver todos os grupos de segurança de rede

Na caixa de pesquisa na parte superior do portal, introduza *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-o. Os grupos de segurança de rede que existem na sua subscrição estão listados.

**Comandos**

- CLI do Azure: [lista de nsg de rede de az](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Ver detalhes de um grupo de segurança de rede

1. Na caixa de pesquisa na parte superior do portal, introduza *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista que pretende ver os detalhes para, selecione o grupo de segurança de rede. Sob **definições** pode ver a **regras de segurança de entrada** e **regras de segurança de saída**, o **interfaces de rede** e  **Sub-redes** o grupo de segurança de rede está associado à. Também pode ativar ou desativar **registos de diagnóstico** e ver **regras de segurança efetivas**. Para obter mais informações, consulte [registos de diagnóstico](virtual-network-nsg-manage-log.md) e [ver as regras de segurança efetivas](diagnose-network-traffic-filter-problem.md).
3. Para saber mais sobre as definições do Azure comuns listadas, veja os artigos seguintes:
    *   [Registo de atividades](../azure-monitor/platform/activity-logs-overview.md)
    *   [Controlo de acesso (IAM)](../role-based-access-control/overview.md)
    *   [Etiquetas](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script de automação](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Comandos**

- CLI do Azure: [show de nsg de rede de az](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Alterar um grupo de segurança de rede

1. Na caixa de pesquisa na parte superior do portal, introduza *grupos de segurança de rede* na caixa de pesquisa. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione o grupo de segurança de rede que pretende alterar. As alterações mais comuns são [adicionando](#create-a-security-rule) ou [removendo](#delete-a-security-rule) regras de segurança e [Associating ou a desassociar a um grupo de segurança de rede para ou a partir de uma interface de rede ou sub-rede](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Comandos**

- CLI do Azure: [atualização de nsg de rede de az](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associar ou desassociar um grupo de segurança de rede para ou a partir de uma interface de rede ou sub-rede

Para associar um grupo de segurança de rede para ou desassociar um grupo de segurança de rede de uma interface de rede, consulte [associar um grupo de segurança de rede para ou desassociar um grupo de segurança de rede de uma interface de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Para associar um grupo de segurança de rede para ou desassociar um grupo de segurança de rede a partir de uma sub-rede, veja [alterar as definições de sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Eliminar um grupo de segurança de rede

Se um grupo de segurança de rede está associado a quaisquer sub-redes ou a interfaces de rede, não pode ser eliminada. Desassociar um grupo de segurança de rede de todas as sub-redes e interfaces de rede antes de tentar eliminá-lo.

1. Na caixa de pesquisa na parte superior do portal, introduza *grupos de segurança de rede* na caixa de pesquisa. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione o grupo de segurança de rede que pretende eliminar a partir da lista.
3. Selecione **elimine**e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [eliminar az rede nsg](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Trabalhar com regras de segurança

Um grupo de segurança de rede contêm zero ou mais regras de segurança. Pode criar, [ver todas](#view-all-security-rules), [ver os detalhes das](#view-details-of-a-security-rule), [alterar](#change-a-security-rule), e [eliminar](#delete-a-security-rule) uma regra de segurança.

### <a name="create-a-security-rule"></a>Criar uma regra de segurança

Existe um limite para quantas regras por grupo de segurança de rede podem criar por subscrição e localização do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa na parte superior do portal, introduza *grupos de segurança de rede* na caixa de pesquisa. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a lista que pretende adicionar uma regra de segurança para o grupo de segurança de rede.
3. Selecione **regras de segurança de entrada** sob **definições**. Várias regras existentes estão listadas. Algumas das regras que não possa ter adicionado. Quando é criado um grupo de segurança de rede, várias regras de segurança predefinidas são criadas na mesma. Para obter mais informações, consulte [regras de segurança predefinidas](security-overview.md#default-security-rules).  Não é possível eliminar regras de segurança predefinidas, mas pode substitui-las com as regras que têm uma prioridade mais alta.
4. <a name = "security-rule-settings"></a>Selecione **+ adicionar**.  Selecionar ou adicionar valores para as seguintes definições e, em seguida, selecione **OK**:
    
    |Definição  |Value  |Detalhes  |
    |---------|---------|---------|
    |Origem     | Selecione **qualquer**, **grupo de segurança de aplicações**, **endereços IP**, ou **etiqueta de serviço** para regras de segurança de entrada. Se estiver a criar uma regra de segurança de saída, as opções são as mesmas opções listadas para **destino**.       | Se selecionou **grupo de segurança de aplicativo**, em seguida, selecione um ou grupos de segurança de aplicação existentes mais que existe na mesma região que a interface de rede. Saiba como [crie um grupo de segurança de aplicações](#create-an-application-security-group). Se selecionou **grupo de segurança de aplicações** para ambos os **origem** e **destino**, as interfaces de rede em ambos os grupos de segurança de aplicação tem de estar na mesma rede virtual. Se selecionou **endereços IP**, em seguida, especifique **intervalos de endereços/CIDR do IP de origem**. Pode especificar um valor único ou uma lista separada por vírgulas de valores múltiplos. Um exemplo de vários valores é 10.0.0.0/16, 192.188.1.1. Existem limites ao número de valores que pode especificar. Ver [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes. Se selecionou **etiquetas de serviço**, em seguida, selecione uma etiqueta de serviço. A etiqueta de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as etiquetas de serviço disponível e o que representa cada marca, veja [etiquetas de serviço](security-overview.md#service-tags). Se o endereço IP que especificou é atribuído a uma máquina virtual do Azure, certifique-se de que especifica o IP privado, não o endereço IP público atribuído à máquina virtual. Regras de segurança são processadas depois Azure traduz-se o endereço IP público para um endereço IP privado para regras de segurança de entrada e antes do Azure traduz-se um endereço IP privado para um endereço IP público para as regras de saída. Para saber mais sobre endereços IP públicos e privados no Azure, veja [tipos de endereços IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Intervalo de portas de origem     | Especifique uma porta única, como 80, um intervalo de portas, por exemplo 1024-65535, ou uma lista separada por vírgulas de portas únicas e/ou intervalos de portas, por exemplo, 80, a 1024 e 65535. Introduza um asterisco para permitir tráfego de qualquer porta. | As portas e intervalos de especificam o tráfego de portas é permitido ou negado pela regra. Existem limites ao número de portas que pode especificar. Ver [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes.  |
    |Destino     | Selecione **qualquer**, **grupo de segurança de aplicações**, **endereços IP**, ou **rede Virtual** para regras de segurança de entrada. Se estiver a criar uma regra de segurança de saída, as opções são as mesmas opções listadas para **origem**.        | Se selecionou **grupo de segurança de aplicativo** , em seguida, tem de selecionar um ou mais aplicações grupos de segurança existentes que existem na mesma região que a interface de rede. Saiba como [crie um grupo de segurança de aplicações](#create-an-application-security-group). Se selecionou **grupo de segurança de aplicativo**, em seguida, selecione um grupo de segurança aplicações existentes que exista na mesma região que a interface de rede. Se selecionou **endereços IP**, em seguida, especifique **intervalos de endereços/CIDR do IP de destino**. Semelhante à **origem** e **intervalos de endereços/CIDR do IP de origem**, pode especificar um único ou vários endereços ou intervalos, e existem limites para o número pode especificar. Selecionando **rede Virtual**, que é uma etiqueta de serviço, significa que o tráfego é permitida para todos os endereços IP dentro do espaço de endereços da rede virtual. Se o endereço IP que especificou é atribuído a uma máquina virtual do Azure, certifique-se de que especifica o IP privado, não o endereço IP público atribuído à máquina virtual. Regras de segurança são processadas depois Azure traduz-se o endereço IP público para um endereço IP privado para regras de segurança de entrada e antes do Azure traduz-se um endereço IP privado para um endereço IP público para as regras de saída. Para saber mais sobre endereços IP públicos e privados no Azure, veja [tipos de endereços IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Intervalos de portas de destino     | Especifique um valor único ou lista separada por vírgulas de valores. | Semelhante à **intervalos de portas de origem**, pode especificar uma única ou várias portas e intervalos, e existem limites para o número pode especificar. |
    |Protocolo     | Selecione **qualquer**, **TCP**, ou **UDP**.        |         |
    |Ação     | Selecione **permitem** ou **negar**.        |         |
    |Prioridade     | Introduza um valor entre 100-4096 que seja exclusivo para todas as regras de segurança dentro do grupo de segurança de rede. |As regras são processadas na ordem de prioridade. Menor o número, maior será a prioridade. Recomenda-se que deixe uma lacuna entre os números de prioridade durante a criação de regras, como 100, 200, 300. Deixa intervalos torna mais fácil de adicionar regras no futuro que talvez precise fazer superior ou inferior do que as regras existentes.         |
    |Name     | Um nome exclusivo para a regra dentro do grupo de segurança de rede.        |  O nome pode ter até 80 carateres. Tem de começar com uma letra ou número, terminar com uma letra, número ou caráter de sublinhado e pode conter apenas letras, números, carateres de sublinhado, pontos ou hífenes.       |
    |Descrição     | Uma descrição opcional.        |         |

**Comandos**

- CLI do Azure: [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Ver todas as regras de segurança

Um grupo de segurança de rede contém zero ou várias regras. Para saber mais sobre as informações indicadas ao visualizar as regras, consulte [descrição geral de grupo de segurança de rede](security-overview.md).

1. Na caixa de pesquisa na parte superior do portal, introduza *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione o grupo de segurança de rede na lista que pretende ver as regras para.
3. Selecione **regras de segurança de entrada** ou **regras de segurança de saída** sob **definições**.

A lista contém quaisquer regras que criou e o grupo de segurança de rede [regras de segurança predefinidas](security-overview.md#default-security-rules).

**Comandos**

- CLI do Azure: [lista de regras de nsg de rede de az](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Ver detalhes de uma regra de segurança

1. Na caixa de pesquisa na parte superior do portal, introduza *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione o grupo de segurança de rede que pretende ver os detalhes de uma regra de segurança para.
3. Selecione **regras de segurança de entrada** ou **regras de segurança de saída** sob **definições**.
4. Selecione a regra que pretende ver os detalhes de. Para obter uma explicação detalhada de todas as definições, consulte [as definições da regra de segurança](#security-rule-settings).

**Comandos**

- CLI do Azure: [show de regra de nsg de rede de az](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Alterar uma regra de segurança

1. Conclua os passos na [ver os detalhes de uma regra de segurança](#view-details-of-a-security-rule).
2. Alterar as definições conforme pretendido e, em seguida, selecione **guardar**. Para obter uma explicação detalhada de todas as definições, consulte [as definições da regra de segurança](#security-rule-settings).

**Comandos**

- CLI do Azure: [atualização de regra de nsg de rede de az](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Eliminar uma regra de segurança

1. Conclua os passos na [ver os detalhes de uma regra de segurança](#view-details-of-a-security-rule).
2. Selecione **elimine**e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [eliminação de regra de nsg de rede de az](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Trabalhar com grupos de segurança de aplicações

Um grupo de segurança do aplicativo contém zero ou mais interfaces de rede. Para obter mais informações, consulte [grupos de segurança de aplicativo](security-overview.md#application-security-groups). Todas as interfaces de rede num grupo de segurança de aplicações tem de existir na mesma rede virtual. Para saber como adicionar uma interface de rede a um grupo de segurança de aplicações, veja [adicionar uma interface de rede a um grupo de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Criar um grupo de segurança da aplicação

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Na caixa **Pesquisar no Marketplace**, introduza *Grupo de segurança de aplicações*. Selecione **Grupo de segurança de aplicações** quando aparecer nos resultados da pesquisa, selecione novamente **Grupo de segurança de aplicações** em **Tudo** e, em seguida, selecione **Criar**.
3. Introduza, ou selecione as seguintes informações e, em seguida, selecione **Criar**:

    | Definição        | Valor                                                   |
    | ---            | ---                                                     |
    | Name           | O nome tem de ser exclusivo dentro de um grupo de recursos.        |
    | Subscrição   | Selecione a sua subscrição.                               |
    | Grupo de recursos | Selecione um grupo de recursos existente ou crie um novo. |
    | Localização       | Selecione uma localização                                       |

**Comandos**

- CLI do Azure: [az rede asg criar](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Ver todos os grupos de segurança de aplicação

1. Selecione **todos os serviços** no canto superior, esquerda do portal do Azure.
2. Introduza *grupos de segurança de aplicativo* no **todos os serviços filtro** caixa e, em seguida, selecione **grupos de segurança de aplicação** quando for apresentada nos resultados da pesquisa.

**Comandos**

- CLI do Azure: [lista de asg de rede de az](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Ver detalhes de um grupo de segurança de aplicação específica

1. Selecione **todos os serviços** no canto superior, esquerda do portal do Azure.
2. Introduza *grupos de segurança de aplicativo* no **todos os serviços filtro** caixa e, em seguida, selecione **grupos de segurança de aplicação** quando for apresentada nos resultados da pesquisa.
3. Selecione o grupo de segurança de aplicação que pretende ver os detalhes de.

**Comandos**

- CLI do Azure: [show de asg de rede de az](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Alterar um grupo de segurança de aplicações

1. Selecione **todos os serviços** no canto superior, esquerda do portal do Azure.
2. Introduza *grupos de segurança de aplicativo* no **todos os serviços filtro** caixa e, em seguida, selecione **grupos de segurança de aplicação** quando for apresentada nos resultados da pesquisa.
3. Selecione o grupo de segurança de aplicação que pretende alterar as definições. Pode adicionar ou remover etiquetas, ou atribuir ou remover permissões para o grupo de segurança de aplicações.

- CLI do Azure: [atualização de asg de rede de az](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Nenhum cmdlet do PowerShell.

### <a name="delete-an-application-security-group"></a>Eliminar um grupo de segurança de aplicações

Não é possível eliminar um grupo de segurança de aplicações, se tiver qualquer interface de rede no mesmo. Remova todas as interfaces de rede do grupo de segurança de aplicação ao alterar as definições de interface de rede ou ao eliminar as interfaces de rede. Para obter detalhes, consulte [adicionar para ou remover uma interface de rede de grupos de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou [eliminar uma interface de rede](virtual-network-network-interface.md#delete-a-network-interface).

1. Selecione **todos os serviços** no canto superior, esquerda do portal do Azure.
2. Introduza *grupos de segurança de aplicativo* no **todos os serviços filtro** caixa e, em seguida, selecione **grupos de segurança de aplicação** quando for apresentada nos resultados da pesquisa.
3. Selecione o grupo de segurança de aplicação que pretende eliminar.
4. Selecione **elimine**e, em seguida, selecione **Sim** para eliminar o grupo de segurança do aplicativo.

**Comandos**

- CLI do Azure: [az rede asg eliminar](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Permissões

Para executar tarefas em grupos de segurança de rede, as regras de segurança e grupos de segurança de aplicações, deve ser atribuída a sua conta para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuído a permissões adequadas listadas nas tabelas seguintes:

### <a name="network-security-group"></a>Grupo de segurança de rede

| Ação                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Obter grupo de segurança de rede                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Criar ou atualizar o grupo de segurança de rede                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Eliminar grupo de segurança de rede                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associar um grupo de segurança de rede a uma interface de rede ou sub-rede 


### <a name="network-security-group-rule"></a>Regra do grupo de segurança de rede

| Ação                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Obter regra                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Criar ou atualizar a regra                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Eliminar regra                                                         |

### <a name="application-security-group"></a>Grupo de segurança de aplicações

| Ação                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Junte-se a uma configuração de IP para um grupo de segurança de aplicações|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Junte-se a uma regra de segurança para um grupo de segurança de aplicações    |
| Microsoft.Network/applicationSecurityGroups/read                           | Obter um grupo de segurança de aplicações                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Criar ou atualizar um grupo de segurança de aplicações           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Eliminar um grupo de segurança de aplicações                     |

## <a name="next-steps"></a>Passos Seguintes

- Criar uma rede ou o grupo de segurança do aplicativo usando [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts ou utilizar o Azure de exemplo [modelos do Resource Manager](template-samples.md)
- Criar e aplicar [o Azure policy](policy-samples.md) para redes virtuais
