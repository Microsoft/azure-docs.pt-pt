---
title: Criar um VM com um endereço IP público estático - Portal Azure / Microsoft Docs
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
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 9e06e4079a5118e0aa9dedb1fca719f0b28e5716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448633"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Criar uma máquina virtual com um endereço IP público estático utilizando o portal Azure

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar a uma máquina virtual a partir da internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca se altera. Saiba mais sobre [endereços IP públicos estáticos.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmica para estática, ou para trabalhar com endereços IP privados, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Os endereços IP públicos têm uma [taxa nominal](https://azure.microsoft.com/pricing/details/ip-addresses), e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que pode utilizar por subscrição.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do portal do Azure.
2. **Selecione Compute**, e, em seguida, selecione **O Windows Server 2016 VM**, ou outro sistema operativo à sua escolha.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Nome|myVM|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Utilizar existente** e selecione **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**.|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. Em **Definições**, selecione **o endereço IP público**.
6. Insira *o myPublicIpAddress,* selecione **Static**, e, em seguida, selecione **OK**, como mostrado na seguinte imagem:

   ![Selecione estática](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Se o endereço IP público tiver de ser um SKU padrão, selecione **Standard** em **SKU**. Saiba mais sobre [o endereço IP PÚBLICO SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual for adicionada ao pool traseiro de um Balançador de Carga Azure público, o SKU do endereço IP público da máquina virtual deve corresponder ao SKU do endereço IP público do balançador de carga. Para mais detalhes, consulte [o Balançador de Carga Azure](../load-balancer/skus.md).

6. Selecione uma porta ou não há portas em **portas de entrada públicas Selecionadas.** O Portal 3389 encontra-se selecionado para permitir o acesso remoto à máquina virtual do Windows Server a partir da internet. A abertura da porta 3389 da internet não é recomendada para cargas de trabalho de produção.

   ![Selecione uma porta](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Aceite as definições predefinitivas restantes e selecione **OK**.
8. Na página **Resumo**, selecione **Criar**. A máquina virtual demora alguns minutos a ser implantada.
9. Assim que a máquina virtual for implantada, insira o *myPublicIpAddress* na caixa de pesquisa no topo do portal. Quando **o meuPublicIpAddress** aparecer nos resultados da pesquisa, selecione-o.
10. Pode visualizar o endereço IP público que é atribuído e que o endereço é atribuído à máquina virtual **myVM,** como mostra a seguinte imagem:

    ![A screenshot mostra o painel de endereços Public I P com o endereço I P e o nome chamado. ](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    O Azure atribuiu um endereço IP público a partir de endereços utilizados na região em que criou a máquina virtual. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

11. Selecione **Configuração** para confirmar que a atribuição é **estática.**

    ![A screenshot mostra o painel de endereços Public I P com o item de Configuração selecionado.](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Não modifique as definições do endereço IP dentro do sistema operativo da máquina virtual. O sistema operativo desconhece os endereços IP públicos do Azure. Embora possa adicionar definições privadas de endereço IP ao sistema operativo, recomendamos que não o faça a menos que seja necessário, e só depois de ler [Adicione um endereço IP privado a um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuindo um [endereço IP estático privado](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual Azure
- Saiba mais sobre a criação de máquinas virtuais [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)