---
title: Criar, alterar ou excluir um grupo de segurança de rede do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar, alterar ou excluir um grupo de segurança de rede.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978935"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Criar, alterar ou excluir um grupo de segurança de rede

As regras de segurança nos grupos de segurança de rede permitem filtrar o tipo de tráfego de rede que pode fluir para dentro e para fora das sub-redes e interfaces de rede da rede virtual. Se você não estiver familiarizado com os grupos de segurança de rede, consulte [visão geral do grupo de segurança de rede](security-overview.md) para saber mais sobre eles e concluir o tutorial [Filtrar tráfego de rede](tutorial-filter-network-traffic.md) para obter alguma experiência com grupos de segurança de rede.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.28 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="work-with-network-security-groups"></a>Trabalhar com grupos de segurança de rede

Você pode criar, [Exibir todos](#view-all-network-security-groups), [Exibir detalhes de](#view-details-of-a-network-security-group), [alterar](#change-a-network-security-group)e [excluir](#delete-a-network-security-group) um grupo de segurança de rede. Você também pode [associar ou dissociar](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) um grupo de segurança de rede de uma interface de rede ou sub-rede.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Há um limite para quantos grupos de segurança de rede você pode criar por assinatura e local do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
2. Selecione **rede**e, em seguida, selecione **grupo de segurança de rede**.
3. Insira um **nome** para o grupo de segurança de rede, selecione sua **assinatura**, crie um novo **grupo de recursos**ou selecione um grupo de recursos existente, selecione um **local**e, em seguida, selecione **criar**.

**Comandos**

- CLI do Azure: [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Exibir todos os grupos de segurança de rede

Na caixa de pesquisa na parte superior do portal, insira *grupos de segurança de rede*. Quando os **grupos de segurança de rede** aparecerem nos resultados da pesquisa, selecione-os. Os grupos de segurança de rede que existem na sua assinatura são listados.

**Comandos**

- CLI do Azure: [AZ Network NSG List](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Exibir detalhes de um grupo de segurança de rede

1. Na caixa de pesquisa na parte superior do portal, insira *grupos de segurança de rede*. Quando os **grupos de segurança de rede** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança de rede na lista para o qual você deseja exibir detalhes. Em **configurações** , você pode exibir as **regras de segurança de entrada** e **as regras de segurança de saída**, as interfaces de **rede** e **sub-redes** às quais o grupo de segurança de rede está associado. Você também pode habilitar ou desabilitar **os logs de diagnóstico** e exibir **regras de segurança em vigor**. Para saber mais, consulte [logs de diagnóstico](virtual-network-nsg-manage-log.md) e [Exibir regras de segurança em vigor](diagnose-network-traffic-filter-problem.md).
3. Para saber mais sobre as configurações comuns do Azure listadas, consulte os seguintes artigos:
    *   [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)
    *   [Controle de acesso (IAM)](../role-based-access-control/overview.md)
    *   [Etiquetas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Bloquea](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script de automação](../azure-resource-manager/templates/export-template-portal.md)

**Comandos**

- CLI do Azure: [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Alterar um grupo de segurança de rede

1. Na caixa de pesquisa na parte superior do portal, insira *grupos de segurança de rede* na caixa de pesquisa. Quando os **grupos de segurança de rede** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança de rede que você deseja alterar. As alterações mais comuns são [Adicionar](#create-a-security-rule) ou [remover](#delete-a-security-rule) regras de segurança e [associar ou dissociar um grupo de segurança de rede de ou para uma sub-rede ou interface de rede](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Comandos**

- CLI do Azure: [AZ Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associar ou dissociar um grupo de segurança de rede de ou para uma sub-rede ou interface de rede

Para associar um grupo de segurança de rede ou dissociar um grupo de segurança de rede de um adaptador de rede, consulte [associar um grupo de segurança de rede ou dissociar um grupo de segurança de rede de uma interface de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Para associar um grupo de segurança de rede ao ou dissociar um grupo de segurança de rede de uma sub-rede, consulte [alterar as configurações de sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Excluir um grupo de segurança de rede

Se um grupo de segurança de rede estiver associado a quaisquer sub-redes ou interfaces de rede, ele não poderá ser excluído. Dissociar um grupo de segurança de rede de todas as sub-redes e interfaces de rede antes de tentar excluí-lo.

1. Na caixa de pesquisa na parte superior do portal, insira *grupos de segurança de rede* na caixa de pesquisa. Quando os **grupos de segurança de rede** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança de rede que você deseja excluir da lista.
3. Selecione **Eliminar** e, em seguida, **Sim**.

**Comandos**

- CLI do Azure: [AZ Network NSG Delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Trabalhar com regras de segurança

Um grupo de segurança de rede contém zero ou mais regras de segurança. Você pode criar, [Exibir todos](#view-all-security-rules), [Exibir detalhes de](#view-details-of-a-security-rule), [alterar](#change-a-security-rule)e [excluir](#delete-a-security-rule) uma regra de segurança.

### <a name="create-a-security-rule"></a>Criar uma regra de segurança

Há um limite para quantas regras por grupo de segurança de rede podem ser criadas por assinatura e local do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa na parte superior do portal, insira *grupos de segurança de rede* na caixa de pesquisa. Quando os **grupos de segurança de rede** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança de rede na lista à qual você deseja adicionar uma regra de segurança.
3. Selecione **regras de segurança de entrada** em **configurações**. Várias regras existentes são listadas. Algumas das regras que você pode não ter adicionado. Quando um grupo de segurança de rede é criado, várias regras de segurança padrão são criadas nele. Para saber mais, consulte [regras de segurança padrão](security-overview.md#default-security-rules).  Não é possível excluir as regras de segurança padrão, mas você pode substituí-las por regras que tenham uma prioridade mais alta.
4. <a name = "security-rule-settings"></a>Selecione **+ Adicionar**.  Selecione ou adicione valores para as seguintes configurações e, em seguida, selecione **OK**:
    
    |Definição  |Valor  |Detalhes  |
    |---------|---------|---------|
    |Origem     | Selecione **qualquer**, **grupo de segurança de aplicativo**, **endereços IP**ou **marca de serviço** para regras de segurança de entrada. Se você estiver criando uma regra de segurança de saída, as opções serão as mesmas que as opções listadas para o **destino**.       | Se você selecionar **grupo de segurança de aplicativo**, selecione um ou mais grupos de segurança de aplicativo existentes que existem na mesma região que a interface de rede. Saiba como [criar um grupo de segurança de aplicativo](#create-an-application-security-group). Se você selecionar **grupo de segurança de aplicativo** para a **origem** e o **destino**, as interfaces de rede dentro dos dois grupos de segurança de aplicativo deverão estar na mesma rede virtual. Se você selecionar **endereços IP**, especifique os **endereços IP de origem/intervalos de CIDR**. Você pode especificar um único valor ou uma lista separada por vírgulas de vários valores. Um exemplo de vários valores é 10.0.0.0/16, 192.188.1.1. Há limites para o número de valores que você pode especificar. Consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes. Se você selecionar **marca de serviço**, selecione uma marca de serviço. Uma marca de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as marcas de serviço disponíveis e o que cada marca representa, consulte [marcas de serviço](security-overview.md#service-tags). Se o endereço IP especificado for atribuído a uma máquina virtual do Azure, certifique-se de especificar o IP privado, não o endereço IP público atribuído à máquina virtual. As regras de segurança são processadas depois que o Azure converte o endereço IP público em um endereço IP privado para regras de segurança de entrada e antes que o Azure traduza um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados no Azure, consulte [tipos de endereço IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Intervalo de portas de origem     | Especifique uma única porta, como 80, um intervalo de portas, como 1024-65535, ou uma lista separada por vírgulas de portas únicas e/ou intervalos de porta, como 80, 1024-65535. Insira um asterisco para permitir o tráfego em qualquer porta. | As portas e os intervalos especificam quais portas o tráfego é permitido ou negado pela regra. Há limites para o número de portas que você pode especificar. Consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes.  |
    |Destino     | Selecione **qualquer**, **grupo de segurança de aplicativo**, **endereços IP**ou **rede virtual** para regras de segurança de saída. Se você estiver criando uma regra de segurança de entrada, as opções serão as mesmas que as opções listadas para **origem**.        | Se você selecionar **grupo de segurança de aplicativo** , deverá selecionar um ou mais grupos de segurança de aplicativo existentes que existem na mesma região que a interface de rede. Saiba como [criar um grupo de segurança de aplicativo](#create-an-application-security-group). Se você selecionar **grupo de segurança de aplicativo**, selecione um grupo de segurança de aplicativo existente que exista na mesma região que a interface de rede. Se você selecionar **endereços IP**, especifique os **endereços IP/intervalos de CIDR de destino**. Semelhante aos intervalos de **CIDR/endereços IP**de **origem e de** origem, você pode especificar um único ou vários endereços ou intervalos, e há limites para o número que você pode especificar. Selecionar **rede virtual**, que é uma marca de serviço, significa que o tráfego é permitido para todos os endereços IP dentro do espaço de endereço da rede virtual. Se o endereço IP especificado for atribuído a uma máquina virtual do Azure, certifique-se de especificar o IP privado, não o endereço IP público atribuído à máquina virtual. As regras de segurança são processadas depois que o Azure converte o endereço IP público em um endereço IP privado para regras de segurança de entrada e antes que o Azure traduza um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados no Azure, consulte [tipos de endereço IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Intervalos de portas de destino     | Especifique um único valor ou uma lista de valores separados por vírgulas. | Semelhante aos **intervalos de porta de origem**, você pode especificar um único, ou várias portas e intervalos, e há limites para o número que você pode especificar. |
    |Protocolo     | Selecione **any**, **TCP**, **UDP** ou **ICMP**.        |         |
    |Ação     | Selecione **permitir** ou **negar**.        |         |
    |Prioridade     | Insira um valor entre 100-4096 que seja exclusivo para todas as regras de segurança no grupo de segurança de rede. |As regras são processadas em ordem de prioridade. Quanto menor o número, maior a prioridade. É recomendável deixar uma lacuna entre os números de prioridade ao criar regras, como 100, 200, 300. Deixar as lacunas facilita a adição de regras no futuro que talvez você precise fazer mais ou menos do que as regras existentes.         |
    |Nome     | Um nome exclusivo para a regra dentro do grupo de segurança de rede.        |  O nome pode ter até 80 caracteres. Ele deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado e pode conter apenas letras, números, sublinhados, pontos ou hifens.       |
    |Descrição     | Uma descrição opcional.        |         |

**Comandos**

- CLI do Azure: [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Exibir todas as regras de segurança

Um grupo de segurança de rede contém zero ou várias regras. Para saber mais sobre as informações listadas ao exibir regras, consulte [visão geral do grupo de segurança de rede](security-overview.md).

1. Na caixa de pesquisa na parte superior do portal, insira *grupos de segurança de rede*. Quando os **grupos de segurança de rede** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança de rede na lista para a qual você deseja exibir regras.
3. Selecione **regras de segurança de entrada** ou **regras de segurança de saída** em **configurações**.

A lista contém todas as regras que você criou e as regras de [segurança padrão](security-overview.md#default-security-rules)do grupo de segurança de rede.

**Comandos**

- CLI do Azure: [AZ Network NSG Rule List](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Ver detalhes de uma regra de segurança

1. Na caixa de pesquisa na parte superior do portal, insira *grupos de segurança de rede*. Quando os **grupos de segurança de rede** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o grupo de segurança de rede para o qual você deseja exibir os detalhes de uma regra de segurança.
3. Selecione **regras de segurança de entrada** ou **regras de segurança de saída** em **configurações**.
4. Selecione a regra para a qual você deseja exibir detalhes. Para obter uma explicação detalhada de todas as configurações, consulte [configurações de regra de segurança](#security-rule-settings).

**Comandos**

- CLI do Azure: [AZ Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Alterar uma regra de segurança

1. Conclua as etapas em [Exibir detalhes de uma regra de segurança](#view-details-of-a-security-rule).
2. Altere as configurações conforme desejado e, em seguida, selecione **salvar**. Para obter uma explicação detalhada de todas as configurações, consulte [configurações de regra de segurança](#security-rule-settings).

**Comandos**

- CLI do Azure: [AZ Network NSG Rule Update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Excluir uma regra de segurança

1. Conclua as etapas em [Exibir detalhes de uma regra de segurança](#view-details-of-a-security-rule).
2. Selecione **Eliminar** e, em seguida, **Sim**.

**Comandos**

- CLI do Azure: [AZ Network NSG Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Trabalhar com grupos de segurança de aplicações

Um grupo de segurança de aplicativo contém zero ou mais interfaces de rede. Para saber mais, confira [grupos de segurança de aplicativo](security-overview.md#application-security-groups). Todas as interfaces de rede em um grupo de segurança de aplicativo devem existir na mesma rede virtual. Para saber como adicionar uma interface de rede a um grupo de segurança de aplicativo, consulte [Adicionar um adaptador de rede a um grupo de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Criar um grupo de segurança de aplicativo

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Na caixa **Pesquisar no Marketplace**, introduza *Grupo de segurança de aplicações*. Selecione **Grupo de segurança de aplicações** quando aparecer nos resultados da pesquisa, selecione novamente **Grupo de segurança de aplicações** em **Tudo** e, em seguida, selecione **Criar**.
3. Introduza, ou selecione as seguintes informações e, em seguida, selecione **Criar**:

    | Definição        | Valor                                                   |
    | ---            | ---                                                     |
    | Nome           | O nome tem de ser exclusivo dentro de um grupo de recursos.        |
    | Subscrição   | Selecione a sua subscrição.                               |
    | Grupo de recursos | Selecione um grupo de recursos existente ou crie um novo. |
    | Localização       | Selecionar uma localização                                       |

**Comandos**

- CLI do Azure: [AZ Network ASG Create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Exibir todos os grupos de segurança de aplicativo

1. Selecione **todos os serviços** no canto superior esquerdo do portal do Azure.
2. Insira *grupos de segurança de aplicativo* na caixa de **filtro todos os serviços** e, em seguida, selecione **grupos de segurança de aplicativo** quando ele aparecer nos resultados da pesquisa.

**Comandos**

- CLI do Azure: [AZ Network ASG List](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Exibir detalhes de um grupo de segurança de aplicativo específico

1. Selecione **todos os serviços** no canto superior esquerdo do portal do Azure.
2. Insira *grupos de segurança de aplicativo* na caixa de **filtro todos os serviços** e, em seguida, selecione **grupos de segurança de aplicativo** quando ele aparecer nos resultados da pesquisa.
3. Selecione o grupo de segurança de aplicativo para o qual você deseja exibir os detalhes.

**Comandos**

- CLI do Azure: [AZ Network ASG show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Alterar um grupo de segurança de aplicativo

1. Selecione **todos os serviços** no canto superior esquerdo do portal do Azure.
2. Insira *grupos de segurança de aplicativo* na caixa de **filtro todos os serviços** e, em seguida, selecione **grupos de segurança de aplicativo** quando ele aparecer nos resultados da pesquisa.
3. Selecione o grupo de segurança de aplicativo para o qual você deseja alterar as configurações. Você pode adicionar ou remover marcas ou atribuir ou remover permissões para o grupo de segurança do aplicativo.

- CLI do Azure: [AZ Network ASG Update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: nenhum cmdlet do PowerShell.

### <a name="delete-an-application-security-group"></a>Excluir um grupo de segurança de aplicativo

Você não poderá excluir um grupo de segurança de aplicativo se ele tiver qualquer interface de rede. Remova todas as interfaces de rede do grupo de segurança de aplicativo alterando as configurações de interface de rede ou excluindo as interfaces de rede. Para obter detalhes, consulte [Adicionar ou remover um adaptador de rede dos grupos de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou [excluir um adaptador de rede](virtual-network-network-interface.md#delete-a-network-interface).

1. Selecione **todos os serviços** no canto superior esquerdo do portal do Azure.
2. Insira *grupos de segurança de aplicativo* na caixa de **filtro todos os serviços** e, em seguida, selecione **grupos de segurança de aplicativo** quando ele aparecer nos resultados da pesquisa.
3. Selecione o grupo de segurança do aplicativo que você deseja excluir.
4. Selecione **excluir**e, em seguida, selecione **Sim** para excluir o grupo de segurança do aplicativo.

**Comandos**

- CLI do Azure: [AZ Network ASG Delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Permissões

Para executar tarefas em grupos de segurança de rede, regras de segurança e grupos de segurança de aplicativo, sua conta deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as permissões apropriadas listadas nas seguintes tabelas:

### <a name="network-security-group"></a>Grupo de segurança de rede

| Ação                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Obter grupo de segurança de rede                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Criar ou atualizar grupo de segurança de rede                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Excluir grupo de segurança de rede                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associar um grupo de segurança de rede a uma sub-rede ou interface de rede 


### <a name="network-security-group-rule"></a>Regra do grupo de segurança de rede

| Ação                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Obter regra                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Criar ou atualizar regra                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Excluir regra                                                         |

### <a name="application-security-group"></a>Grupo de segurança de aplicativo

| Ação                                                                     | Nome                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Unir uma configuração de IP a um grupo de segurança de aplicativo|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Unir uma regra de segurança a um grupo de segurança de aplicativo    |
| Microsoft.Network/applicationSecurityGroups/read                           | Obter um grupo de segurança de aplicativo                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Criar ou atualizar um grupo de segurança de aplicativo           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Excluir um grupo de segurança de aplicativo                     |

## <a name="next-steps"></a>Passos seguintes

- Criar um grupo de segurança de aplicativo ou de rede usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou usando modelos do Azure [Resource Manager](template-samples.md)
- Criar e aplicar a [política do Azure](policy-samples.md) para redes virtuais
