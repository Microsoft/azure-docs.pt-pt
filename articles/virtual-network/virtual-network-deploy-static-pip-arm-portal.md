---
title: Criar um VM com um endereço IP público estático - Portal Azure
description: Saiba como criar um VM com um endereço IP público estático utilizando o portal Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: d416af3d3a8eb8ab8057f13cc0d9a133adcb849a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221161"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Criar uma máquina virtual com um endereço IP público estático utilizando o portal Azure

Um endereço IP público permite-lhe comunicar a uma máquina virtual a partir da internet. 

Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca se altera.   

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual compute de recurso ou procurar por máquina  >    >   **Virtual** na caixa de pesquisa.
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **Criar novo**. </br> Em **Nome,** insira **o myResourceGroup**. </br> Selecione **OK**. |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM** |
    | Region | Selecione **E.U.A. Leste**. |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **Windows Server 2019 Datacenter - Gen1** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Aceite o nome de rede padrão. |
    | Sub-rede | Aceite a configuração de sub-rede predefinido. |
    | IP público | Selecione **Criar novo**. </br> In **Create public IP address**, in name in **myPublicIP**. </br> Para **SKU**, selecione **Standard**. </br> **Atribuição**, selecione **Estática**. </br> Selecione **OK**.  |
    | Grupo de segurança de rede NIC | Selecione **Basic**|
    | Portas de entrada públicas | Selecione **Deixe as portas selecionadas.** |
    | Selecione as portas de entrada | Selecione **RDP (3389)** |

    > [!WARNING]
    > O Portal 3389 encontra-se selecionado para permitir o acesso remoto à máquina virtual do Windows Server a partir da internet. A abertura da porta 3389 à internet não é recomendada para gerir cargas de trabalho de produção. </br> Para acesso seguro às máquinas virtuais Azure, veja **[o que é Azure Bastion?](../bastion/bastion-overview.md)**
   
5. Selecione **Rever + criar**. 
  
6. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza **myResourceGroup** na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza **myResourceGroup** em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md):

* Para alterar um endereço IP público de dinâmico para estático.
* Trabalhe com endereços IP privados.

Os endereços IP públicos têm uma [taxa nominal.](https://azure.microsoft.com/pricing/details/ip-addresses) Há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que pode usar por subscrição.

O SKU do endereço IP público da máquina virtual deve corresponder ao IP PÚBLICO SKU do Azure Load Balancer quando adicionado a um pool de backend. Para mais detalhes, consulte [o Balançador de Carga Azure](../load-balancer/skus.md).

Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

- Saiba mais sobre [endereços IP públicos estáticos.](./public-ip-addresses.md#allocation-method)
- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Saiba mais sobre [endereços IP privados](./private-ip-addresses.md) e atribuindo um [endereço IP estático privado](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual Azure.
