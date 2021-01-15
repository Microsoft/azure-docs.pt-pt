---
title: Criar, alterar ou eliminar um grupo de segurança da rede Azure
titlesuffix: Azure Virtual Network
description: Saiba onde encontrar informações sobre regras de segurança e como criar, alterar ou eliminar um grupo de segurança de rede.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d42e0f5594fdde55f1b4183a806e388658e86dc3
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222943"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Criar, alterar ou eliminar um grupo de segurança de rede

As regras de segurança nos grupos de segurança da rede permitem filtrar o tipo de tráfego de rede que pode fluir dentro e fora das sub-redes de rede virtuais e interfaces de rede. Para saber mais sobre os grupos de segurança de rede, consulte [a visão geral do grupo de segurança da rede.](./network-security-groups-overview.md) Em seguida, complete o tutorial [de tráfego da rede Filter](tutorial-filter-network-traffic.md) para ganhar alguma experiência com grupos de segurança de rede.

## <a name="before-you-begin"></a>Before you begin

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma, crie uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Complete uma destas tarefas antes de iniciar o restante deste artigo:

- **Utilizadores do Portal**: Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

- **Utilizadores powerShell**: Ou executam os comandos na [Azure Cloud Shell,](https://shell.azure.com/powershell)ou executam o PowerShell a partir do computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. No separador de navegador Azure Cloud Shell, encontre a lista de dropdown **de ambiente Select** e, em seguida, escolha **PowerShell** se ainda não estiver selecionado.

    Se estiver a executar o PowerShell localmente, utilize a versão 1.0.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Execute `Connect-AzAccount` para criar uma ligação com o Azure.

- **Os utilizadores da interface de linha de comando azure (CLI):** Ou executam os comandos na [Azure Cloud Shell,](https://shell.azure.com/bash)ou executam o CLI a partir do computador. Utilize a versão 2.0.28 do Azure CLI ou mais tarde se estiver a executar o Azure CLI localmente. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Execute `az login` para criar uma ligação com o Azure.

A conta em que inicia sessão ou liga-se ao Azure deve ser atribuída à [função de contribuinte da Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações [apropriadas listadas](#permissions)nas Permissões .

## <a name="work-with-network-security-groups"></a>Trabalhar com grupos de segurança de rede

Pode criar, [ver todos,](#view-all-network-security-groups) [ver detalhes de,](#view-details-of-a-network-security-group) [alterar,](#change-a-network-security-group)e [excluir](#delete-a-network-security-group) um grupo de segurança de rede. Também pode [associar ou dissociar](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) um grupo de segurança de rede a partir de uma interface de rede ou sub-rede.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Há um limite para quantos grupos de segurança de rede pode criar para cada localização e subscrição do Azure. Para saber mais, consulte [os limites de subscrição e serviço da Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. No menu do [portal do Azure](https://portal.azure.com) ou a partir da **Home page**, selecione **Criar um recurso**.

2. Selecione **Networking** e, em seguida, selecione **grupo de segurança de rede**.

3. Na página do grupo de segurança da **rede Criar,** no separador **Básicos,** definir os valores para as seguintes definições:

    | Definição | Ação |
    | --- | --- |
    | **Subscrição** | Escolha a sua subscrição. |
    | **Grupo de recursos** | Escolha um grupo de recursos existente ou selecione **Criar novo** para criar um novo grupo de recursos. |
    | **Nome** | Introduza uma cadeia de texto única dentro de um grupo de recursos. |
    | **Região** | Escolha o local que deseja. |

4. Selecione **Rever + criar**.

5. Depois de ver a mensagem **passada da Validação,** selecione **Criar**.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [Grupo deSegurança New-AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Ver todos os grupos de segurança da rede

Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos de segurança da Rede.** A lista de grupos de segurança de rede aparece para a sua subscrição.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [lista nsg de rede az](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Ver detalhes de um grupo de segurança de rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos de segurança da Rede.**

2. Selecione o nome do seu grupo de segurança de rede.

Na barra de menu do grupo de segurança de rede, em **Definições,** pode ver **as regras de segurança de entrada,** **regras de segurança de saída,** **interfaces de rede** e **subnetas** a que o grupo de segurança da rede está associado.

Em **Monitorização,** pode ativar ou desativar **as definições de Diagnóstico**. Em **Suporte + resolução de problemas,** pode ver **regras de segurança eficazes**. Para saber mais, consulte [o registo de diagnóstico de um grupo de segurança de rede](virtual-network-nsg-manage-log.md) e [diagnostice um problema de filtro de tráfego de rede VM](diagnose-network-traffic-filter-problem.md).

Para saber mais sobre as definições comuns do Azure listadas, consulte os seguintes artigos:

- [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)
- [Controlo de acesso (IAM)](../role-based-access-control/overview.md)
- [Etiquetas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Bloqueios](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script de automatização](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Alterar um grupo de segurança de rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos de segurança da Rede.**

2. Selecione o nome do grupo de segurança de rede que pretende alterar.

As alterações mais comuns são [adicionar uma regra de segurança](#create-a-security-rule), remover uma [regra](#delete-a-security-rule), e associar [ou dissociar um grupo de segurança de rede para ou de uma interface de sub-rede ou rede](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [atualização nsg de rede az](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associar ou dissociar um grupo de segurança de rede para ou de uma interface de sub-rede ou rede

Para associar um grupo de segurança de rede ou dissociar um grupo de segurança de rede a partir de uma interface de rede, consulte [associar um grupo de segurança de rede para, ou dissociar um grupo de segurança de rede a partir de uma interface de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Para associar um grupo de segurança de rede ou dissociar um grupo de segurança de rede a partir de uma sub-rede, consulte [as definições da sub-rede Change](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Excluir um grupo de segurança de rede

Se um grupo de segurança de rede estiver associado a quaisquer sub-redes ou interfaces de rede, não pode ser eliminado. Dissociem um grupo de segurança de rede de todas as sub-redes e interfaces de rede antes de tentar eliminá-lo.

1. Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos de segurança da Rede.**

2. Selecione o nome do grupo de segurança de rede que pretende eliminar.

3. Na barra de ferramentas do grupo de segurança de rede, selecione **Delete**. Em seguida, selecione **Sim** na caixa de diálogo de confirmação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg eliminar](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Trabalhar com regras de segurança

Um grupo de segurança de rede contém zero ou mais regras de segurança. Pode criar, [ver tudo,](#view-all-security-rules) [ver detalhes de,](#view-details-of-a-security-rule) [alterar,](#change-a-security-rule)e [apagar](#delete-a-security-rule) uma regra de segurança.

### <a name="create-a-security-rule"></a>Criar uma regra de segurança

Há um limite para quantas regras por grupo de segurança de rede pode criar para cada localização e subscrição do Azure. Para saber mais, consulte [os limites de subscrição e serviço da Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de rede. Procure e selecione **grupos de segurança da Rede.**

2. Selecione o nome do grupo de segurança de rede a que pretende adicionar uma regra de segurança.

3. Na barra de menus do grupo de segurança da rede, escolha **as regras de segurança de Entrada** ou **regras de segurança de saída**.

    Várias regras existentes estão listadas, incluindo algumas que pode não ter adicionado. Quando cria um grupo de segurança de rede, são criadas várias regras de segurança padrão no mesmo. Para saber mais, consulte [as regras de segurança predefinidos.](./network-security-groups-overview.md#default-security-rules)  Não pode eliminar as regras de segurança predefinidos, mas pode anuê-las com regras que têm uma prioridade maior.

4. <a name="security-rule-settings"></a>Selecione **Adicionar**. Selecione ou adicione valores para as seguintes definições e, em seguida, selecione **OK**:

    | Definição | Valor | Detalhes |
    | ------- | ----- | ------- |
    | **Origem** | Um dos seguintes:<ul><li>**Qualquer**</li><li>**Endereços IP**</li><li>**Tag de serviço** (regra de segurança de entrada) ou **VirtualNetwork** (regra de segurança de saída)</li><li>**Grupo &nbsp; de segurança de &nbsp; aplicação**</li></ul> | <p>Se escolher **endereços IP,** também deve especificar **endereços IP/gamas CIDR** de origem .</p><p>Se escolher **a Etiqueta de Serviço,** também pode escolher uma etiqueta de serviço **Source**.</p><p>Se escolher **o grupo de segurança de aplicação,** também deve escolher um grupo de segurança de aplicações existente. Se escolher **o grupo de segurança da aplicação** tanto para a **Fonte** como para o **Destino,** as interfaces de rede dentro de ambos os grupos de segurança de aplicações devem estar na mesma rede virtual.</p> |
    | **Endereços IP de origem/gamas CIDR** | Uma lista delimitada por vírgula de endereços IP e gamas de encaminhamento interdomínio sem classe (CIDR) | <p>Esta definição aparece se alterar **Fonte** para **endereços IP**. Deve especificar uma lista de valores separados por um único valor ou vírgula de vários valores. Um exemplo de múltiplos valores `10.0.0.0/16, 192.188.1.1` é. Existem limites para o número de valores que pode especificar. Para mais detalhes, consulte [os limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Se o endereço IP que especificar estiver atribuído a um VM Azure, especifique o seu endereço IP privado e não o seu endereço IP público. O Azure processa as regras de segurança depois de traduzir o endereço IP público para um endereço IP privado para regras de segurança de entrada, mas antes de traduzir um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados em Azure, consulte [os tipos de endereços IP](./public-ip-addresses.md).</p> |
    | **Etiqueta de serviço de origem** | Uma etiqueta de serviço da lista de dropdown | Esta definição opcional aparece se definir **Source** to **Service Tag** para uma regra de segurança de entrada. Uma etiqueta de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as etiquetas de serviço disponíveis, e o que cada tag representa, consulte [as etiquetas de serviço](./network-security-groups-overview.md#service-tags). |
    | **Grupo de segurança de aplicações de origem** | Um grupo de segurança de aplicações existente | Esta definição aparece se definir o grupo de segurança **Source** to **Application**. Selecione um grupo de segurança de aplicações que exista na mesma região que a interface de rede. Saiba como [criar um grupo de segurança de aplicações.](#create-an-application-security-group) |
    | **Intervalo de portas de origem** | Um dos seguintes:<ul><li>Um único porto, como `80`</li><li>Uma gama de portos, tais como `1024-65535`</li><li>Uma lista separada por vírgula de portas individuais e/ou gamas portuárias, tais como `80, 1024-65535`</li><li>Um asterisco `*` para permitir o tráfego em qualquer porto</li></ul> | Esta definição especifica as portas em que a regra permite ou nega o tráfego. Há limites para o número de portas que pode especificar. Para mais detalhes, consulte [os limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Destino** | Um dos seguintes:<ul><li>**Qualquer**</li><li>**Endereços IP**</li><li>**Tag de serviço** (regra de segurança de saída) ou **VirtualNetwork** (regra de segurança de entrada)</li><li>**Grupo &nbsp; de segurança de &nbsp; aplicação**</li></ul> | <p>Se escolher **endereços IP,** então especifique também **endereços IP de destino/gamas CIDR**.</p><p>Se escolher **a VirtualNetwork,** é permitido tráfego a todos os endereços IP dentro do espaço de endereço da rede virtual. **VirtualNetwork** é uma etiqueta de serviço.</p><p>Se selecionar **o grupo de segurança de aplicação,** deve então selecionar um grupo de segurança de aplicação existente. Saiba como [criar um grupo de segurança de aplicações.](#create-an-application-security-group)</p> |
    | **Endereços IP de destino/gamas CIDR** | Uma lista delimitada por vírgula de endereços IP e gamas CIDR | <p>Esta definição aparece se alterar **destino** para **endereços IP**. Semelhante aos endereços **IP de Origem** e **Fonte/Gamas CIDR,** pode especificar endereços ou intervalos únicos ou múltiplos. Há limites para o número que pode especificar. Para mais detalhes, consulte [os limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Se o endereço IP que especificar estiver atribuído a um VM Azure, certifique-se de que especifica o seu IP privado e não o seu endereço IP público. O Azure processa as regras de segurança depois de traduzir o endereço IP público para um endereço IP privado para regras de segurança de entrada, mas antes que o Azure traduz um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados em Azure, consulte [os tipos de endereços IP](./public-ip-addresses.md).</p> |
    | **Etiqueta do serviço de destino** | Uma etiqueta de serviço da lista de dropdown | Esta definição opcional aparece se alterar **Destination** to **Service Tag** para uma regra de segurança de saída. Uma etiqueta de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as etiquetas de serviço disponíveis, e o que cada tag representa, consulte [as etiquetas de serviço](./network-security-groups-overview.md#service-tags). |
    | **Grupo de segurança de aplicação de destino** | Um grupo de segurança de aplicações existente | Esta definição aparece se definir **o grupo** de segurança Destino para **Aplicação**. Selecione um grupo de segurança de aplicações que exista na mesma região que a interface de rede. Saiba como [criar um grupo de segurança de aplicações.](#create-an-application-security-group) |
    | **Intervalos de portas de destino** | Um dos seguintes:<ul><li>Um único porto, como `80`</li><li>Uma gama de portos, tais como `1024-65535`</li><li>Uma lista separada por vírgula de portas individuais e/ou gamas portuárias, tais como `80, 1024-65535`</li><li>Um asterisco `*` para permitir o tráfego em qualquer porto</li></ul> | Tal como acontece com **as gamas de portas Source,** pode especificar portas e gamas simples ou múltiplas. Há limites para o número que pode especificar. Para mais detalhes, consulte [os limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Protocolo** | **Qualquer**, **TCP,** **UDP,** ou **ICMP** | Pode restringir a regra ao Protocolo de Controlo de Transmissão (TCP), ao Protocolo de Datagrama do Utilizador (UDP) ou ao Protocolo de Mensagens de Controlo de Internet (ICMP). O padrão é que a regra se aplique a todos os protocolos. |
    | **Ação** | **Permitir** ou **Negar** | Esta definição especifica se esta regra permite ou nega o acesso para a configuração fornecida da fonte e destino. |
    | **Priority** | Um valor entre 100 e 4096 que é único para todas as regras de segurança dentro do grupo de segurança da rede | Azure processa as regras de segurança em ordem prioritária. Quanto menor for o número, maior é a prioridade. Recomendamos que deixe uma lacuna entre números prioritários quando criar regras, tais como 100, 200 e 300. Deixar lacunas facilita a adição de regras no futuro, para que possa dar-lhes maior ou menor prioridade do que as regras existentes. |
    | **Nome** | Um nome único para a regra dentro do grupo de segurança da rede | O nome pode chegar aos 80 caracteres. Deve começar com uma letra ou número, e deve terminar com uma letra, número ou sublinhado. O nome pode conter apenas letras, números, sublinhados, períodos ou hífenes. |
    | **Descrição** | Uma descrição de texto | Pode especificar opcionalmente uma descrição de texto para a regra de segurança. A descrição não pode ter mais de 140 caracteres. |

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Ver todas as regras de segurança

Um grupo de segurança de rede contém zero ou mais regras. Para saber mais sobre as informações listadas ao visualizar as regras, consulte [a visão geral do grupo de segurança da rede](./network-security-groups-overview.md).

1. Vá ao [portal Azure](https://portal.azure.com) para ver as regras de um grupo de segurança de rede. Procure e selecione **grupos de segurança da Rede.**

2. Selecione o nome do grupo de segurança de rede para o quais deseja ver as regras.

3. Na barra de menus do grupo de segurança da rede, escolha **as regras de segurança de Entrada** ou **regras de segurança de saída**.

A lista contém quaisquer regras que tenha criado e as [regras](./network-security-groups-overview.md#default-security-rules)de segurança do grupo de segurança da rede.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Ver detalhes de uma regra de segurança

1. Vá ao [portal Azure](https://portal.azure.com) para ver as regras de um grupo de segurança de rede. Procure e selecione **grupos de segurança da Rede.**

2. Selecione o nome do grupo de segurança de rede para o quais deseja visualizar os detalhes de uma regra.

3. Na barra de menus do grupo de segurança da rede, escolha **as regras de segurança de Entrada** ou **regras de segurança de saída**.

4. Selecione a regra para a quais deseja ver detalhes. Para obter uma explicação de todas as definições, consulte as [definições da regra de segurança](#security-rule-settings).

    > [!NOTE]
    > Este procedimento aplica-se apenas a uma regra de segurança personalizada. Não funciona se escolher uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg regra show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Alterar uma regra de segurança

1. Preencha os passos na [Vista detalhes de uma regra de segurança](#view-details-of-a-security-rule).

2. Altere as definições conforme necessário e, em seguida, **selecione Guardar**. Para obter uma explicação de todas as definições, consulte as [definições da regra de segurança](#security-rule-settings).

    > [!NOTE]
    > Este procedimento aplica-se apenas a uma regra de segurança personalizada. Não podes alterar uma regra de segurança por defeito.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Eliminar uma regra de segurança

1. Preencha os passos na [Vista detalhes de uma regra de segurança](#view-details-of-a-security-rule).

2. Selecione **Eliminar** e, em seguida, **Sim**.

    > [!NOTE]
    > Este procedimento aplica-se apenas a uma regra de segurança personalizada. Não está autorizado a apagar uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [regra nsg de rede az eliminar](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remover-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Trabalhar com grupos de segurança de aplicações

Um grupo de segurança de aplicações contém interfaces de rede zero ou mais. Para saber mais, consulte [os grupos de segurança de aplicações.](./network-security-groups-overview.md#application-security-groups) Todas as interfaces de rede de um grupo de segurança de aplicações devem existir na mesma rede virtual. Para aprender a adicionar uma interface de rede a um grupo de segurança de aplicações, consulte [Adicionar uma interface de rede a um grupo de segurança de aplicações.](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)

### <a name="create-an-application-security-group"></a>Criar um grupo de segurança de aplicações

1. No menu do [portal do Azure](https://portal.azure.com) ou a partir da **Home page**, selecione **Criar um recurso**.

2. Na caixa de pesquisa, insira *o grupo de segurança aplicação*.

3. Na página do **grupo de segurança aplicação,** selecione **Criar**.

4. Na página Criar um grupo de segurança de **aplicações,** no separador **Básicos,** definir valores para as seguintes definições:

    | Definição | Ação |
    | --- | --- |
    | **Subscrição** | Escolha a sua subscrição. |
    | **Grupo de recursos** | Escolha um grupo de recursos existente ou selecione **Criar novo** para criar um novo grupo de recursos. |
    | **Nome** | Introduza uma cadeia de texto única dentro de um grupo de recursos. |
    | **Região** | Escolha o local que deseja. |

5. Selecione **Rever + criar**.

6. No **separador 'Rever + criar',** depois de ver a mensagem **de validação passada,** selecione **Criar**.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg criar](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [Grupo deSegurança new-AzApplication](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Ver todos os grupos de segurança de aplicações

Vá ao [portal Azure](https://portal.azure.com) para ver os seus grupos de segurança de aplicações. Procure e selecione **grupos de segurança da aplicação.** O portal Azure apresenta uma lista dos grupos de segurança da sua aplicação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [lista de asg da rede az](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Grupo de Segurança Get-AzApplication](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Ver detalhes de um grupo específico de segurança de aplicações

1. Vá ao [portal Azure](https://portal.azure.com) para ver um grupo de segurança de aplicações. Procure e selecione **grupos de segurança da aplicação.**

2. Selecione o nome do grupo de segurança de aplicações que deseja ver os detalhes de.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Grupo de Segurança Get-AzApplication](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Alterar um grupo de segurança de aplicações

1. Vá ao [portal Azure](https://portal.azure.com) para ver um grupo de segurança de aplicações. Procure e selecione **grupos de segurança da aplicação.**

2. Selecione o nome do grupo de segurança de aplicação que pretende alterar.

3. Selecione **a alteração** ao lado da definição que pretende modificar. Por exemplo, pode adicionar ou remover **Tags,** ou pode alterar o **grupo de Recursos** ou **Subscrição.**

    > [!NOTE]
    > Não pode mudar o local.

    Na barra de menus, também pode selecionar **o controlo de acesso (IAM)**. Na página **Access control (IAM),** pode atribuir ou remover permissões ao grupo de segurança da aplicação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [atualização da rede az asg](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Sem cmdlet PowerShell |

### <a name="delete-an-application-security-group"></a>Excluir um grupo de segurança de aplicações

Não é possível eliminar um grupo de segurança de aplicações se contiver interfaces de rede. Para remover todas as interfaces de rede do grupo de segurança da aplicação, altere as definições da interface de rede ou elimine as interfaces de rede. Para saber mais, consulte [Adicionar ou remover dos grupos de segurança da aplicação](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou eliminar uma interface de [rede.](virtual-network-network-interface.md#delete-a-network-interface)

1. Vá ao [portal Azure](https://portal.azure.com) para gerir os seus grupos de segurança de aplicações. Procure e selecione **grupos de segurança da aplicação.**

2. Selecione o nome do grupo de segurança de aplicações que pretende eliminar.

3. Selecione **Eliminar** e, em seguida, selecione **Sim** para eliminar o grupo de segurança da aplicação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg eliminar](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Permissões

Para realizar tarefas em grupos de segurança de rede, regras de segurança e grupos de segurança de aplicações, a sua conta deve ser atribuída à [função de contribuinte da Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as permissões apropriadas, conforme listado nas seguintes tabelas:

### <a name="network-security-group"></a>Grupo de segurança de rede

| Ação                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Obtenha grupo de segurança de rede                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Criar ou atualizar grupo de segurança de rede                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Eliminar grupo de segurança de rede                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associar um grupo de segurança de rede a uma interface de sub-rede ou rede 

### <a name="network-security-group-rule"></a>Regra do grupo de segurança da rede

| Ação                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   Obtenha a regra                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   Criar ou atualizar regra                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   Eliminar Regra                                                         |

### <a name="application-security-group"></a>Grupo de segurança de aplicações

| Ação                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Junte-se a uma configuração IP a um grupo de segurança de aplicações|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Junte-se a uma regra de segurança a um grupo de segurança de aplicações    |
| Microsoft.Network/applicationSecurityGroups/ler                           | Obtenha um grupo de segurança de aplicações                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Criar ou atualizar um grupo de segurança de aplicações           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Excluir um grupo de segurança de aplicações                     |

## <a name="next-steps"></a>Passos seguintes

- Crie um grupo de segurança de rede ou aplicação utilizando scripts de [amostras powerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou [modelos de Gestor de Recursos](template-samples.md) Azure
- Criar e atribuir [definições de Política Azure](./policy-reference.md) para redes virtuais