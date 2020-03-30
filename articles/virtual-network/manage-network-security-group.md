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
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: a22adef5510e24c2dc07ffb39c9687d500644f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066441"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Criar, alterar ou eliminar um grupo de segurança de rede

As regras de segurança nos grupos de segurança da rede permitem filtrar o tipo de tráfego de rede que pode fluir para dentro e para fora de subredes de rede virtuais e interfaces de rede. Para saber mais sobre os grupos de segurança da rede, consulte a visão geral do grupo de [segurança da rede](security-overview.md). Em seguida, complete o tutorial de tráfego da [rede Filter](tutorial-filter-network-traffic.md) para obter alguma experiência com grupos de segurança da rede.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma, instale uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Complete uma destas tarefas antes de iniciar o restante deste artigo:

- **Utilizadores**do portal : Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

- **Utilizadores powerShell**: Ou executa os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/powershell)ou executa powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. No separador de navegador Azure Cloud Shell, encontre a lista de abandono ambiental **Select** e escolha **powerShell** se ainda não estiver selecionado.

    Se estiver a executar o PowerShell localmente, utilize a versão 1.0.0 do módulo PowerShell Do Mato Azure. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Execute `Connect-AzAccount` para criar uma ligação com o Azure.

- Utilizadores da **interface da linha de comando Azure (CLI):** Ou executa os comandos na Membrana Nuvem [Azure,](https://shell.azure.com/bash)ou executa o CLI a partir do seu computador. Utilize a versão Azure CLI 2.0.28 ou mais tarde se estiver a executar o Azure CLI localmente. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Execute `az login` para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure deve ser atribuída à [função](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de colaborador da Rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações apropriadas listadas nas [Permissões](#permissions).

## <a name="work-with-network-security-groups"></a>Trabalhar com grupos de segurança de rede

Pode criar, [visualizar todos, ver](#view-all-network-security-groups) [detalhes de,](#view-details-of-a-network-security-group) [alterar](#change-a-network-security-group)e [eliminar](#delete-a-network-security-group) um grupo de segurança da rede. Também pode [associar ou dissociar](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) um grupo de segurança de rede a partir de uma interface de rede ou subnet.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Há um limite para quantos grupos de segurança de rede você pode criar para cada localização e subscrição Do Azure. Para saber mais, consulte [limites de subscrição e serviço do Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. No menu do [portal Azure](https://portal.azure.com) ou na página **Inicial,** selecione **Criar um recurso**.

2. **Selecione Networking**, então selecione o grupo de **segurança da rede**.

3. Na página do grupo de segurança da **rede Create,** sob o separador **Basics,** definir valores para as seguintes definições:

    | Definição | Ação |
    | --- | --- |
    | **Assinatura** | Escolha a sua subscrição. |
    | **Grupo de recursos** | Escolha um grupo de recursos existente, ou selecione **Criar novo** para criar um novo grupo de recursos. |
    | **Nome** | Introduza uma cadeia de texto única dentro de um grupo de recursos. |
    | **Região** | Escolha o local que deseja. |

4. Selecione **Rever + criar**.

5. Depois de ver a mensagem **aprovada pela Validação,** selecione **Criar**.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Ver todos os grupos de segurança da rede

Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos**de segurança da Rede . A lista de grupos de segurança da rede aparece para a sua subscrição.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az lista de nsg rede](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Ver detalhes de um grupo de segurança de rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos**de segurança da Rede .

2. Selecione o nome do seu grupo de segurança de rede.

Na barra de menus do grupo de segurança da rede, em **Definições,** pode ver as regras de **segurança de entrada,** regras de segurança de **saída,** **interfaces**de rede e **Subnets** a que o grupo de segurança da rede está associado.

Sob **monitorização,** pode ativar ou desativar **as definições**de diagnóstico . Sob **suporte + resolução de problemas,** pode ver regras de **segurança eficazes.** Para saber mais, consulte o [diagnóstico de registo de registo sintetização de um grupo](virtual-network-nsg-manage-log.md) de segurança de rede e diagnostice um problema de filtro de tráfego de rede [VM](diagnose-network-traffic-filter-problem.md).

Para saber mais sobre as definições comuns do Azure listadas, consulte os seguintes artigos:

- [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)
- [Controlo de acesso (IAM)](../role-based-access-control/overview.md)
- [Etiquetas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Fechaduras](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script de automatização](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg mostrar](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Alterar um grupo de segurança de rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos**de segurança da Rede .

2. Selecione o nome do grupo de segurança da rede que pretende alterar.

As alterações mais comuns são adicionar uma regra de [segurança,](#create-a-security-rule) [remover uma regra,](#delete-a-security-rule)e [associar ou dissociar um grupo de segurança de rede de ou de uma interface de rede ou sub-rede](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg atualização](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associar ou dissociar um grupo de segurança de rede de ou para uma interface de rede ou subnet

Para associar um grupo de segurança de rede a, ou dissociar um grupo de segurança de rede de uma interface de rede, consulte associate um grupo de [segurança de rede para, ou dissociar um grupo de segurança de rede de uma interface de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Para associar um grupo de segurança de rede ou dissociar um grupo de segurança de rede de uma subnet, consulte [as definições da sub-rede Change](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Eliminar um grupo de segurança de rede

Se um grupo de segurança de rede estiver associado a quaisquer subredes ou interfaces de rede, não pode ser eliminado. Dissociar um grupo de segurança de rede de todas as subredes e interfaces de rede antes de tentar eliminá-lo.

1. Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos**de segurança da Rede .

2. Selecione o nome do grupo de segurança da rede que pretende eliminar.

3. Na barra de ferramentas do grupo de segurança da rede, selecione **Eliminar**. Em seguida, selecione **Sim** na caixa de diálogo de confirmação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg excluir](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remover-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Trabalhar com regras de segurança

Um grupo de segurança de rede contém zero ou mais regras de segurança. Pode criar, [ver todos, ver](#view-all-security-rules)detalhes [de,](#view-details-of-a-security-rule) [alterar](#change-a-security-rule)e [eliminar](#delete-a-security-rule) uma regra de segurança.

### <a name="create-a-security-rule"></a>Criar uma regra de segurança

Há um limite para quantas regras por grupo de segurança de rede você pode criar para cada localização e subscrição Do Azure. Para saber mais, consulte [limites de subscrição e serviço do Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos**de segurança da Rede .

2. Selecione o nome do grupo de segurança da rede a que pretende adicionar uma regra de segurança.

3. Na barra de menus do grupo de segurança da rede, escolha regras de **segurança de entrada** ou regras de segurança de **saída**.

    Várias regras existentes estão listadas, incluindo algumas que talvez não tenha adicionado. Quando se cria um grupo de segurança de rede, são criadas nele várias regras de segurança padrão. Para saber mais, consulte as regras de [segurança padrão](security-overview.md#default-security-rules).  Não pode eliminar regras de segurança por defeito, mas pode substituí-las com regras que têm uma prioridade maior.

4. <a name="security-rule-settings"></a>**Selecione Adicionar**. Selecione ou adicione valores para as seguintes definições e, em seguida, selecione **OK**:

    | Definição | Valor | Detalhes |
    | ------- | ----- | ------- |
    | **Origem** | Um dos:<ul><li>**Qualquer**</li><li>**Endereços IP**</li><li>**Etiqueta de serviço** (regra de segurança de entrada) ou **VirtualNetwork** (regra de segurança de saída)</li><li>**Grupo&nbsp;&nbsp;de segurança de aplicações**</li></ul> | <p>Se escolher **endereços IP,** também deve especificar **endereços IP/intervalos CIDR**.</p><p>Se escolher **a etiqueta de serviço,** também pode escolher uma etiqueta de serviço **Fonte**.</p><p>Se escolher o grupo de **segurança da Aplicação,** também deve escolher um grupo de segurança de aplicações existente. Se escolher o grupo de **segurança aplicação** tanto para **origem** como **para destino,** as interfaces de rede em ambos os grupos de segurança de aplicações devem estar na mesma rede virtual.</p> |
    | **Endereços IP de origem/gamas CIDR** | Uma lista de endereços IP deslimitada sem víreu e gamas de encaminhamento de interdomínio sem classe (CIDR) | <p>Esta definição aparece se alterar os **endereços** **Source** para IP . Deve especificar uma única lista de valores separados ou de vários valores. Um exemplo de `10.0.0.0/16, 192.188.1.1`múltiplos valores é. Existem limites para o número de valores que pode especificar. Para mais detalhes, consulte [os limites de Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Se o endereço IP que especifica for atribuído a um VM Azure, especifique o seu endereço IP privado e não o seu endereço IP público. O Azure processa as regras de segurança depois de traduzir o endereço IP público para um endereço IP privado para regras de segurança de entrada, mas antes de traduzir um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados em Azure, consulte os tipos de [endereços IP](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Etiqueta de serviço de origem** | Uma etiqueta de serviço da lista de dropdown | Esta definição opcional aparece se definir a Etiqueta **Fonte** para **o Serviço** para uma regra de segurança de entrada. Uma etiqueta de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as etiquetas de serviço disponíveis e sobre o que cada etiqueta representa, consulte etiquetas de [serviço.](security-overview.md#service-tags) |
    | **Grupo de segurança de aplicações de origem** | Um grupo de segurança de aplicações existente | Esta definição aparece se definir o grupo de segurança **Source** to **Application**. Selecione um grupo de segurança de aplicações que exista na mesma região que a interface de rede. Saiba como criar um grupo de segurança de [aplicações.](#create-an-application-security-group) |
    | **Intervalo de portas de origem** | Um dos:<ul><li>Um único porto, como`80`</li><li>Uma gama de portas, tais como`1024-65535`</li><li>Uma lista separada das portas únicas e/ou das faixas portuárias, tais como`80, 1024-65535`</li><li>Um asterisco`*`para permitir o tráfego em qualquer porto</li></ul> | Esta definição especifica as portas nas quais a regra permite ou nega o tráfego. Existem limites para o número de portas que pode especificar. Para mais detalhes, consulte [os limites de Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Destino** | Um dos:<ul><li>**Qualquer**</li><li>**Endereços IP**</li><li>**Etiqueta de serviço** (regra de segurança de saída) ou **VirtualNetwork** (regra de segurança de entrada)</li><li>**Grupo&nbsp;&nbsp;de segurança de aplicações**</li></ul> | <p>Se escolher **endereços IP,** então também especifique **endereços IP de destino/gamas CIDR**.</p><p>Se escolher **virtualNetwork,** o tráfego é permitido a todos os endereços IP dentro do espaço de endereço da rede virtual. **VirtualNetwork** é uma etiqueta de serviço.</p><p>Se selecionar o grupo de **segurança da Aplicação,** deve selecionar um grupo de segurança de aplicações existente. Saiba como criar um grupo de segurança de [aplicações.](#create-an-application-security-group)</p> |
    | **Endereços IP de destino/gamas CIDR** | Uma lista de endereços IP e CIDR de limitado em víreu | <p>Esta definição aparece se mudar o **Destino** para **endereços IP**. Semelhante aos **endereços IP/gamas CIDR**de **Origem** e Origem, pode especificar endereços ou intervalos únicos ou múltiplos. Há limites para o número que pode especificar. Para mais detalhes, consulte [os limites de Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Se o endereço IP que especifica for atribuído a um VM Azure, certifique-se de que especifica o seu IP privado, e não o seu endereço IP público. O Azure processa as regras de segurança depois de traduzir o endereço IP público para um endereço IP privado para regras de segurança de entrada, mas antes do Azure traduzir um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados em Azure, consulte os tipos de [endereços IP](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Etiqueta do serviço de destino** | Uma etiqueta de serviço da lista de dropdown | Esta definição opcional aparece se mudar **o Destino** para etiqueta de **serviço** para uma regra de segurança de saída. Uma etiqueta de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as etiquetas de serviço disponíveis e sobre o que cada etiqueta representa, consulte etiquetas de [serviço.](security-overview.md#service-tags) |
    | **Grupo de segurança de aplicações de destino** | Um grupo de segurança de aplicações existente | Esta definição aparece se definir o grupo de segurança **Destino** para **aplicação**. Selecione um grupo de segurança de aplicações que exista na mesma região que a interface de rede. Saiba como criar um grupo de segurança de [aplicações.](#create-an-application-security-group) |
    | **Intervalos de portas de destino** | Um dos:<ul><li>Um único porto, como`80`</li><li>Uma gama de portas, tais como`1024-65535`</li><li>Uma lista separada das portas únicas e/ou das faixas portuárias, tais como`80, 1024-65535`</li><li>Um asterisco`*`para permitir o tráfego em qualquer porto</li></ul> | Tal como acontece com as gamas de **portas Source,** pode especificar portas e gamas únicas ou múltiplas. Há limites para o número que pode especificar. Para mais detalhes, consulte [os limites de Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Protocolo** | **Qualquer,** **TCP,** **UDP**ou **ICMP** | Pode restringir a regra ao Protocolo de Controlo de Transmissão (TCP), Protocolo de Datagram de Utilizador (UDP) ou Protocolo de Mensagem de Controlo de Internet (ICMP). O padrão é que a regra se aplique a todos os protocolos. |
    | **Ação** | **Permitir** ou **Negar** | Esta definição especifica se esta regra permite ou nega o acesso à configuração de origem e destino fornecida. |
    | **Prioridade** | Um valor entre 100 e 4096 que é único para todas as regras de segurança dentro do grupo de segurança da rede | Azure processa regras de segurança por ordem prioritária. Quanto menor o número, maior a prioridade. Recomendamos que deixe uma lacuna entre números prioritários quando cria regras, como 100, 200 e 300. Deixar lacunas facilita a adição de regras no futuro, para que possa dar-lhes uma prioridade maior ou menor do que as regras existentes. |
    | **Nome** | Um nome único para a regra dentro do grupo de segurança da rede | O nome pode ser de até 80 caracteres. Deve começar com uma letra ou número, e deve terminar com uma letra, número ou sublinhado. O nome pode conter apenas letras, números, sublinhados, períodos ou hífenes. |
    | **Descrição** | Uma descrição de texto | Pode especificar opcionalmente uma descrição de texto para a regra de segurança. |

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Ver todas as regras de segurança

Um grupo de segurança de rede contém zero ou mais regras. Para saber mais sobre as informações listadas ao visualizar as regras, consulte a visão geral do [grupo de segurança da rede](security-overview.md).

1. Vá ao [portal Azure](https://portal.azure.com) para ver as regras de um grupo de segurança de rede. Procure e selecione **grupos**de segurança da Rede .

2. Selecione o nome do grupo de segurança da rede para o que pretende ver as regras.

3. Na barra de menus do grupo de segurança da rede, escolha regras de **segurança de entrada** ou regras de segurança de **saída**.

A lista contém quaisquer regras que tenha criado e as regras de [segurança padrão](security-overview.md#default-security-rules)do grupo de segurança da rede.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Ver detalhes de uma regra de segurança

1. Vá ao [portal Azure](https://portal.azure.com) para ver as regras de um grupo de segurança de rede. Procure e selecione **grupos**de segurança da Rede .

2. Selecione o nome do grupo de segurança da rede para o quais pretende ver os detalhes de uma regra.

3. Na barra de menus do grupo de segurança da rede, escolha regras de **segurança de entrada** ou regras de segurança de **saída**.

4. Selecione a regra para a sua visualização. Para obter uma explicação de todas as definições, consulte [as definições](#security-rule-settings)da regra de segurança .

    > [!NOTE]
    > Este procedimento aplica-se apenas a uma regra de segurança personalizada. Não funciona se escolheres uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg regra mostrar](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Alterar uma regra de segurança

1. Complete os passos em [Ver detalhes de uma regra de segurança](#view-details-of-a-security-rule).

2. Altere as definições conforme necessário e, em seguida, selecione **Guardar**. Para obter uma explicação de todas as definições, consulte [as definições](#security-rule-settings)da regra de segurança .

    > [!NOTE]
    > Este procedimento aplica-se apenas a uma regra de segurança personalizada. Não pode alterar uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Eliminar uma regra de segurança

1. Complete os passos em [Ver detalhes de uma regra de segurança](#view-details-of-a-security-rule).

2. Selecione **Eliminar** e, em seguida, **Sim**.

    > [!NOTE]
    > Este procedimento aplica-se apenas a uma regra de segurança personalizada. Não está autorizado a apagar uma regra de segurança por defeito.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg regra excluir](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remover-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Trabalhar com grupos de segurança de aplicações

Um grupo de segurança de aplicações contém zero ou mais interfaces de rede. Para saber mais, consulte os grupos de segurança da [aplicação.](security-overview.md#application-security-groups) Todas as interfaces de rede num grupo de segurança de aplicações devem existir na mesma rede virtual. Para aprender a adicionar uma interface de rede a um grupo de segurança de aplicações, consulte Adicionar uma interface de rede a um grupo de segurança de [aplicações](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Criar um grupo de segurança de aplicações

1. No menu do [portal Azure](https://portal.azure.com) ou na página **Inicial,** selecione **Criar um recurso**.

2. Na caixa de pesquisa, introduza o *grupo de segurança Aplicação*.

3. Na página do grupo de **segurança da Aplicação,** selecione **Criar**.

4. Na página Criar uma página de grupo de segurança de **aplicações,** sob o separador **Basics,** definir valores para as seguintes definições:

    | Definição | Ação |
    | --- | --- |
    | **Assinatura** | Escolha a sua subscrição. |
    | **Grupo de recursos** | Escolha um grupo de recursos existente, ou selecione **Criar novo** para criar um novo grupo de recursos. |
    | **Nome** | Introduza uma cadeia de texto única dentro de um grupo de recursos. |
    | **Região** | Escolha o local que deseja. |

5. Selecione **Rever + criar**.

6. Sob o **separador Review + criar** separador, depois de ver a mensagem **de validação passada,** selecione **Criar**.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg criar](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Ver todos os grupos de segurança de aplicações

Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de aplicações. Procure e selecione grupos de **segurança da Aplicação**. O portal Azure apresenta uma lista dos seus grupos de segurança de aplicação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az lista asg rede](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Ver detalhes de um grupo específico de segurança de aplicações

1. Vá ao [portal Azure](https://portal.azure.com) para ver um grupo de segurança de aplicações. Procure e selecione grupos de **segurança da Aplicação**.

2. Selecione o nome do grupo de segurança da aplicação que pretende ver os detalhes.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Alterar um grupo de segurança de aplicações

1. Vá ao [portal Azure](https://portal.azure.com) para ver um grupo de segurança de aplicações. Procure e selecione grupos de **segurança da Aplicação**.

2. Selecione o nome do grupo de segurança da aplicação que pretende alterar.

3. Selecione **alterar** ao lado da definição que pretende modificar. Por exemplo, pode adicionar ou remover **Etiquetas,** ou pode alterar o **grupo Derecurso** ou **subscrição**.

    > [!NOTE]
    > Não pode mudar o local.

    Na barra de menus, também pode selecionar **o controlo de acesso (IAM)**. Na página de controlo de **acesso (IAM),** pode atribuir ou remover permissões ao grupo de segurança da aplicação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg atualização](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Sem cmdlet PowerShell |

### <a name="delete-an-application-security-group"></a>Eliminar um grupo de segurança de aplicações

Não é possível eliminar um grupo de segurança de aplicações se contiver quaisquer interfaces de rede. Para remover todas as interfaces de rede do grupo de segurança da aplicação, altere as definições da interface da rede ou elimine as interfaces de rede. Para saber mais, consulte [Adicionar ou remover dos grupos de segurança da aplicação](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou eliminar uma interface de [rede](virtual-network-network-interface.md#delete-a-network-interface).

1. Vá ao [portal Azure](https://portal.azure.com) para gerir os seus grupos de segurança de aplicações. Procure e selecione grupos de **segurança da Aplicação**.

2. Selecione o nome do grupo de segurança da aplicação que pretende eliminar.

3. **Selecione Eliminar**e, em seguida, selecione **Sim** para eliminar o grupo de segurança da aplicação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg apagar](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remover-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Permissões

Para realizar tarefas em grupos de segurança de rede, regras de segurança e grupos de segurança de aplicações, a sua conta deve ser atribuída ao papel de contribuinte da [Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as permissões apropriadas listadas nas seguintes tabelas:

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
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Eliminar Regra                                                         |

### <a name="application-security-group"></a>Grupo de segurança de aplicações

| Ação                                                                     | Nome                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Junte-se a uma configuração IP para um grupo de segurança de aplicações|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Aderir a uma regra de segurança a um grupo de segurança de aplicações    |
| Microsoft.Network/applicationSecurityGroups/read                           | Obtenha um grupo de segurança de aplicações                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Criar ou atualizar um grupo de segurança de aplicações           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Eliminar um grupo de segurança de aplicações                     |

## <a name="next-steps"></a>Passos seguintes

- Criar um grupo de segurança de rede ou aplicação utilizando scripts de amostra [PowerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou [modelos](template-samples.md) de Gestor de Recursos Azure
- Criar e aplicar [a política azure](policy-samples.md) para redes virtuais
