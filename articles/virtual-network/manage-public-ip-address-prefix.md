---
title: Criar, alterar ou eliminar um prefixo de endereço IP público do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar, alterar ou eliminar um prefixo de endereço IP público.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: ece6a6efa2f4424fb1c9d7f5a7e12a4e707faf45
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649310"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Criar, alterar ou eliminar um prefixo de endereço IP público

Saiba mais sobre um prefixo de endereço IP público e como criar, alterar e eliminar um. Um prefixo de endereço IP público é um intervalo contíguo de endereços com base no número de endereços IP públicos que especificar. Os endereços são atribuídos à sua subscrição. Quando cria um recurso de endereço IP público, pode atribuir um endereço IP público estático a partir do prefixo e associar o endereço para máquinas virtuais, balanceadores de carga ou outros recursos, para permitir a conectividade de internet. Se não estiver familiarizado com prefixos de endereço IP públicos, consulte o artigo [descrição geral de prefixo de endereço IP público](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Antes de começar

> [!IMPORTANT]
> Prefixo de IP público é em pré-visualização pública em regiões limitadas. Pode [saber o que significa no modo de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Prefixo do IP público está atualmente disponível em: EUA Centro-Oeste, E.U.A. oeste, E.U.A. oeste 2, E.U.A. Central, Europa do Norte, Europa Ocidental e Sudeste asiático. Para obter uma lista atualizada de regiões, consulte [atualizações do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a versão 2.0.41 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

A conta iniciar sessão no, ou ligar ao Azure, tem de ser atribuída para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [permissões ](#permissions).

Prefixos de endereços IP públicos têm um custo. Para obter detalhes, consulte [preços](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Criar um prefixo de endereço IP público

1. No canto superior, à esquerda do portal, selecione **+ criar um recurso**.
2. Introduza *prefixo de endereço ip público* no *pesquisar no Marketplace* caixa. Quando **prefixo de endereço IP público** aparecer nos resultados da pesquisa, selecione.
3. Sob **prefixo de endereço IP público**, selecione **criar**.
4. Introduza ou selecione os valores para as seguintes definições em **criar prefixo de endereço IP público**, em seguida, selecione **criar**:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Subscrição|Sim|Tem de existir na mesma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) como o recurso que pretende associar o endereço IP público a.|
   |Grupo de recursos|Sim|Pode existir na mesma ou diferentes, [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) como o recurso que pretende associar o endereço IP público a.|
   |Name|Sim|O nome tem de ser exclusivo no grupo de recursos que selecionar.|
   |Região|Sim|Tem de existir na mesma [região](https://azure.microsoft.com/regions)como os endereços IP públicos irá atribuir endereços do intervalo. Prefixo é atualmente é a pré-visualização nos e.u.a. centro-oeste, E.U.A. oeste, E.U.A. oeste 2, E.U.A. Central, Europa do Norte, Europa Ocidental e Sudeste asiático.|
   |Tamanho do prefixo|Sim| O tamanho do prefixo que é necessário. R/28 ou 16 endereços IP é a predefinição.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Criar um endereço IP público estático a partir de um prefixo
Depois de criar um prefixo, tem de criar o endereços IP estáticos a partir do prefixo. Para fazer isso, siga os passos abaixo.

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *prefixo de endereço ip público*. Quando **prefixos de endereço IP público** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione o prefixo que pretende criar IPs públicos do.
3. Quando for apresentada nos resultados da pesquisa, selecione-o e clique em **+ adicionar endereço IP** na seção de visão geral. No caso de não ver isso, certifique-se de que está a utilizar a ligação correta para a pré-visualização: https://aka.ms/publicipprefixportal
4. Introduza ou selecione os valores para as seguintes definições sob **Criar endereço IP público**. Uma vez que é um prefixo para o Standard SKU, IPv4 e estático, só precisa de fornecer as seguintes informações:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Name|Sim|O nome do endereço IP público tem de ser exclusivo no grupo de recursos que selecionar.|
   |Tempo limite de inatividade (minutos)|Não|O número de minutos para manter uma ligação TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep-alive. |
   |Etiqueta de nome DNS|Não|Tem de ser exclusivo dentro da região do Azure, que crie o nome de no (em todas as subscrições e todos os clientes). Azure regista automaticamente o nome e endereço IP no seu DNS para que possa ligar a um recurso com o nome. Azure acrescenta uma sub-rede de predefinição como *location.cloudapp.azure.com* (onde a localização é a localização que selecionou) para o nome que indicar, para criar o nome DNS completamente qualificado. Para obter mais informações, consulte [Use Azure DNS com um endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

## <a name="view-or-delete-a-prefix"></a>Ver ou eliminar um prefixo

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *prefixo de endereço ip público*. Quando **prefixos de endereço IP público** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione o nome do prefixo de endereço IP público que pretende ver, alterar as definições para ou eliminar da lista.
3. Realize uma das seguintes opções, dependendo se pretende ver, eliminar ou alterar o prefixo de endereço IP público.
   - **Vista**: O **descrição geral** secção mostra as definições de chave para o prefixo de endereço IP público, tais como prefixo.
   - **Delete**: Para eliminar o prefixo de endereço IP público, selecione **elimine** no **descrição geral** secção. Se os endereços dentro do prefixo estão associados a recursos de endereço IP público, tem primeiro de eliminar os recursos de endereço IP públicos. Ver [eliminar um endereço IP público](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[lista de prefixos de public-ip de rede de AZ](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) a lista os endereços IP públicos, [show de prefixo de ip público de rede de az](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) para mostrar as definições; [atualizações de prefixo de ip público de rede de az](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) atualizar; [delete de prefixo de ip público de rede de az](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) para eliminar|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) para obter um objeto de endereço IP público e ver as respetivas definições, [conjunto AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) ao atualizar as definições; [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) para eliminar|

## <a name="permissions"></a>Permissões

Para executar tarefas em prefixos de endereço IP públicos, sua conta tem de ser atribuída ao [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) função que é atribuída as ações adequadas listadas na tabela a seguir:

| Ação                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Ler um prefixo de endereço IP público                                |
| Microsoft.Network/publicIPPrefixes/write                          | Criar ou atualizar um prefixo de endereço IP público                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Eliminar um prefixo de endereço IP público                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Criar um endereço IP público a partir de um prefixo |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre cenários e vantagens da utilização de um [prefixo do IP público](public-ip-address-prefix.md)