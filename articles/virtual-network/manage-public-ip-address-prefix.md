---
title: Criar, alterar ou eliminar um prefixo de endereço IP público Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre os prefixos de endereço IP públicos e como criá-los, alterá-los ou eliminá-los. Veja onde encontrar informações adicionais.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 2e32faad698fbf316d51123cc8b7845a3b262c7f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938657"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Criar, alterar ou eliminar um prefixo de endereço IP público

Saiba mais sobre um prefixo de endereço IP público e como criar, alterar e apagar um. Um prefixo de endereço IP público é um leque contíguo de endereços baseados no número de endereços IP públicos que especifica. Os endereços são atribuídos à sua subscrição. Ao criar um recurso de endereço IP público, pode atribuir um endereço IP público estático a partir do prefixo e associar o endereço a máquinas virtuais, equilibradores de carga ou outros recursos, para permitir a conectividade da Internet. Se não estiver familiarizado com os prefixos do endereço IP público, consulte a [visão geral do prefixo do endereço IP público](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar etapas em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se para uma [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.com e faça login com a sua conta Azure.
- Se utilizar comandos PowerShell para completar tarefas neste artigo, ou executa os comandos na [Azure Cloud Shell](https://shell.azure.com/powershell), ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar os comandos da interface da linha de comando do Azure (CLI) para completar as tarefas neste artigo, ou executar os comandos na [Azure Cloud Shell](https://shell.azure.com/bash), ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.41 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações [apropriadas listadas](#permissions)nas Permissões .

Os prefixos de endereço IP públicos têm uma taxa. Para mais detalhes, consulte [os preços.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address-prefix"></a>Criar um prefixo de endereço IP público

1. Na parte superior, canto esquerdo do portal, selecione **+ Criar um recurso.**
2. Introduza *o prefixo ip público* na caixa *'Pesquisar o Mercado'.* Quando **o prefixo do endereço IP público** aparecer nos resultados da pesquisa, selecione-o.
3. No **prefixo do endereço IP público,** selecione **Criar**.
4. Introduza ou selecione valores para as seguintes definições, no **prefixo do endereço IP público,** em seguida, selecione **Criar**:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Subscrição|Yes|Deve existir na mesma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso a que pretende associar o endereço IP público.|
   |Grupo de recursos|Yes|Pode existir no mesmo grupo de [recursos,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) ou diferente, que o recurso a que pretende associar o endereço IP público.|
   |Name|Yes|O nome deve ser único dentro do grupo de recursos que seleciona.|
   |Region|Yes|Deve existir na mesma [região](https://azure.microsoft.com/regions)que os endereços IP públicos que irá atribuir endereços do intervalo.|
   |Tamanho do prefixo|Yes| O tamanho do prefixo que precisa. A /28 ou 16 endereços IP é o predefinido.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[prefixo az rede pública-ip criar](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Novo-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>Em regiões com zonas de disponibilidade, pode utilizar comandos PowerShell ou CLI para criar um prefixo de endereço IP público como: não-zonal, associado a uma zona específica, ou para utilizar a redundância de zona.  Para a versão API 2020-08-01 ou posterior, se não for fornecido um parâmetro de zona, é criado um prefixo de endereço IP público não zonal. Para versões da API com mais de 2020-08-01, é criado um prefixo de endereço IP público redundante. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Criar um endereço IP público estático a partir de um prefixo
Uma vez criado um prefixo, deve criar endereços IP estáticos a partir do prefixo. Para isso, siga os passos abaixo.

1. Na caixa que contém o texto *Procure os recursos* no topo do portal Azure, *digite prefixo de endereço ip público*. Quando **os prefixos do endereço IP público** aparecerem nos resultados da pesquisa, selecione-o.
2. Selecione o prefixo de onde pretende criar iPs públicos a partir de.
3. Quando aparecer nos resultados da pesquisa, selecione-o e clique no **endereço IP +Add** na secção Vista Geral.
4. Introduza ou selecione valores para as seguintes definições no **endereço IP público**. Uma vez que um prefixo é para Standard SKU, IPv4 e estática, só precisa fornecer as seguintes informações:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Name|Yes|O nome do endereço IP público deve ser único dentro do grupo de recursos que seleciona.|
   |Tempo de 20 minutos (minutos)|No|Quantos minutos para manter aberta uma ligação TCP ou HTTP sem depender dos clientes para enviar mensagens de vida. |
   |Etiqueta de nome DNS|No|Deve ser único na região Azure em que cria o nome (em todas as subscrições e todos os clientes). O Azure regista automaticamente o nome e o endereço IP no seu DNS para que possa ligar-se a um recurso com o nome. O Azure anexa uma sub-rede predefinida, como *location.cloudapp.azure.com* (onde a localização é a localização selecionada) para o nome que fornece, para criar o nome DNS totalmente qualificado. Para mais informações, consulte [O Azure DNS com um endereço IP público Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Em alternativa, pode utilizar os comandos CLI e PS abaixo com os parâmetros --public-ip-prefix (CLI) e -PublicIpPrefix (PS), para criar um recurso de endereço IP público. 

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Ver ou apagar um prefixo

1. Na caixa que contém o texto *Procure os recursos* no topo do portal Azure, *digite prefixo de endereço ip público*. Quando **os prefixos do endereço IP público** aparecerem nos resultados da pesquisa, selecione-o.
2. Selecione o nome do prefixo do endereço IP público que pretende visualizar, alterar definições ou eliminar da lista.
3. Preencha uma das seguintes opções, dependendo se pretende ver, excluir ou alterar o prefixo do endereço IP público.
   - **Ver**: A secção **'Visão Geral'** mostra as definições das chaves para o prefixo do endereço IP público, como o prefixo.
   - **Excluir**: Para eliminar o prefixo do endereço IP público, **selecione Eliminar** na secção **'Vista Geral'.** Se os endereços dentro do prefixo estiverem associados a recursos de endereço IP públicos, deve primeiro eliminar os recursos de endereço IP público. Consulte [um endereço IP público](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[lista de prefixos ip públicos da rede az](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) para listar endereços IP públicos, [programa de prefixo ip de rede az](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) para mostrar configurações; [atualização do prefixo de ip da rede az](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) para atualizar; [prefixo público-ip da rede az para](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) eliminar para eliminar|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) para recuperar um objeto de endereço IP público e ver as suas definições, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) para atualizar as definições; [Remove-AzPublicIpPrefix para](/powershell/module/az.network/remove-azpublicipprefix) eliminar|

## <a name="permissions"></a>Permissões

Para executar tarefas em prefixos de endereços IP públicos, a sua conta deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações apropriadas listadas no quadro seguinte:

| Ação                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/ler                           | Leia um prefixo de endereço IP público                                |
| Microsoft.Network/publicIPPrefixes/write                          | Criar ou atualizar um prefixo de endereço IP público                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Eliminar um prefixo de endereço IP público                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Criar um endereço IP público a partir de um prefixo |

## <a name="next-steps"></a>Próximos passos

- Saiba sobre cenários e benefícios de usar um [prefixo IP público](public-ip-address-prefix.md)
