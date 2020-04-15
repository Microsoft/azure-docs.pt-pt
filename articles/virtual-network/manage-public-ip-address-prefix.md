---
title: Criar, alterar ou eliminar um prefixo de endereço IP público do Azure
titlesuffix: Azure Virtual Network
description: Aprenda a criar, alterar ou eliminar um prefixo de endereço IP público.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: anavin
ms.openlocfilehash: 5f0c2d9757f3652b0f83b8c36d89c855f7a92fdd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383873"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Criar, alterar ou eliminar um prefixo de endereço IP público

Saiba mais sobre um prefixo de endereço IP público e como criar, alterar e apagar um. Um prefixo de endereço IP público é uma gama contígua de endereços baseados no número de endereços IP públicos que especifica. Os endereços são atribuídos à sua subscrição. Ao criar um recurso público de endereço IP, pode atribuir um endereço IP público estático do prefixo e associar o endereço a máquinas virtuais, equilibradores de carga ou outros recursos, para permitir a conectividade da Internet. Se não estiver familiarizado com prefixos de endereço IP públicos, consulte a visão geral do [endereço IP público](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o https://portal.azure.comportal, abra e faça login na sua conta Azure.
- Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.41 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI `az login` localmente, também precisa de correr para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas nas [Permissões](#permissions).

Os prefixos de endereçoIP públicotêm uma taxa. Para mais detalhes, consulte [os preços.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address-prefix"></a>Criar um prefixo de endereço IP público

1. Na parte superior, canto esquerdo do portal, selecione **+ Criar um recurso**.
2. Introduza *prefixo ip público* na caixa *De mercado.* Quando o **prefixo** de endereço IP público aparecer nos resultados da pesquisa, selecione-o.
3. Sob prefixo de **endereço IP público,** selecione **Criar**.
4. Introduza ou selecione valores para as seguintes definições, sob a **pré-fixação de endereçoip público**e, em seguida, selecione **Criar:**

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Subscrição|Sim|Deve existir na mesma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso a que pretende associar o endereço IP público.|
   |Grupo de recursos|Sim|Pode existir no mesmo grupo de [recursos,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) ou diferente, como o recurso a que pretende associar o endereço IP público.|
   |Nome|Sim|O nome deve ser único dentro do grupo de recursos que selecionar.|
   |Região|Sim|Deve existir na mesma [região](https://azure.microsoft.com/regions)que os endereços IP públicos que atribuirá endereços a partir do intervalo.|
   |Tamanho de prefixo|Sim| O tamanho do prefixo que precisa. Um endereço IP /28 ou 16 é o padrão.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az rede público-ip prefixo criar](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Novo AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Criar um endereço IP público estático a partir de um prefixo
Uma vez criado um prefixo, deve criar endereços IP estáticos a partir do prefixo. Para isso, siga os passos abaixo.

1. Na caixa que contém os recursos de *pesquisa* de texto no topo do portal Azure, digite *prefixo de endereço ip público*. Quando os **prefixos** de endereço IP públicos aparecerem nos resultados da pesquisa, selecione-o.
2. Selecione o prefixo de que pretende criar IPs públicos a partir de.
3. Quando aparecer nos resultados da pesquisa, selecione-o e clique em **+Adicionar endereço IP** na secção Visão Geral.
4. Introduza ou selecione valores para as seguintes definições sob **o endereço IP Criar público**. Uma vez que um prefixo é para Standard SKU, IPv4 e estática, você só precisa fornecer as seguintes informações:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Nome|Sim|O nome do endereço IP público deve ser único dentro do grupo de recursos que selecionar.|
   |Tempo limite (minutos)|Não|Quantos minutos para manter aberta uma ligação TCP ou HTTP sem depender dos clientes para enviar mensagens de "keep-alive". |
   |Etiqueta de nome DNS|Não|Deve ser único dentro da região de Azure em que cria o nome (em todas as subscrições e todos os clientes). O Azure regista automaticamente o nome e o endereço IP no seu DNS para que possa ligar-se a um recurso com o nome. O Azure atribua uma sub-rede predefinida como *location.cloudapp.azure.com* (onde a localização é a localização que seleciona) ao nome que fornece, para criar o nome DNS totalmente qualificado. Para mais informações, consulte [Use Azure DNS com um endereço IP público Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Em alternativa, pode utilizar os comandos CLI e PS abaixo com os parâmetros --public-ip-prefixo (CLI) e -PublicIpPrefix (PS), para criar um recurso de endereço IP público. 

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Ver ou eliminar um prefixo

1. Na caixa que contém os recursos de *pesquisa* de texto no topo do portal Azure, digite *prefixo de endereço ip público*. Quando os **prefixos** de endereço IP públicos aparecerem nos resultados da pesquisa, selecione-o.
2. Selecione o nome do prefixo do endereço IP público que pretende visualizar, alterar as definições ou eliminar da lista.
3. Complete uma das seguintes opções, dependendo se pretende ver, eliminar ou alterar o prefixo de endereço IP público.
   - **Ver**: A secção **'Visão Geral'** mostra as definições de chave para o prefixo do endereço IP público, como prefixo.
   - **Eliminar**: Para eliminar o prefixo do endereço IP público, **selecione Eliminar** na secção **Visão Geral.** Se os endereços dentro do prefixo estiverem associados aos recursos públicos de endereço IP, deve primeiro eliminar os recursos de endereço IP públicos. Consulte [a eliminação de um endereço IP público](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[Az rede lista de prefixos públicos ip](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) para listar endereços IP públicos, programa de [prefixo público-ip](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) da rede az para mostrar configurações; [Az rede public-ip pré-fixação atualização;](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) [az rede público-ip prefixo excluir](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) para apagar|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) para recuperar um objeto de endereço IP público e visualizar as suas definições, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) para atualizar definições; [Remover AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) para eliminar|

## <a name="permissions"></a>Permissões

Para executar tarefas em prefixos de endereçoip públicos, a sua conta deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações apropriadas enumeradas na tabela seguinte:

| Ação                                                            | Nome                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Leia um prefixo de endereço IP público                                |
| Microsoft.Network/publicIPPrefixes/write                          | Criar ou atualizar um prefixo de endereço IP público                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Eliminar um prefixo de endereço IP público                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Criar um endereço IP público a partir de um prefixo |

## <a name="next-steps"></a>Passos seguintes

- Conheça cenários e benefícios de usar um [prefixo IP público](public-ip-address-prefix.md)
