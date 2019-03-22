---
title: Criar uma VM com um endereço IP público estático - portal do Azure | Documentos da Microsoft
description: Saiba como criar uma VM com um endereço IP público estático através do portal do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 42e035b9dca6168fe77e6982505692cf18bfcb40
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106428"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Criar uma máquina virtual com um endereço IP público estático através do portal do Azure

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar a uma máquina virtual a partir da internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca é alterado. Saiba mais sobre [endereços IP públicos estáticos](virtual-network-ip-addresses-overview-arm.md#allocation-method). Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmico para estático, ou para trabalhar com endereços IP privados, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Endereços IP públicos têm um [encargo nominal](https://azure.microsoft.com/pricing/details/ip-addresses)e há um [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ao número de endereços IP públicos, que podem ser utilizados por subscrição.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do Portal do Azure.
2. Selecione **computação**e, em seguida, selecione **VM do Windows Server 2016**, ou outro sistema operacional à sua escolha.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Name|myVM|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Utilizar existente** e selecione **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. Sob **configurações**, selecione **endereço IP público**.
6. Introduza *myPublicIpAddress*, selecione **estático**e, em seguida, selecione **OK**, conforme mostrado na imagem seguinte:

   ![Selecione estático](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Se o endereço IP público tem de ser um SKU standard, selecione **padrão** sob **SKU**. Saiba mais sobre [endereço IP público SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual será adicionada ao agrupamento de back-end de um balanceador de carga do Azure público, o SKU de endereço IP público da máquina virtual tem de corresponder ao SKU do endereço IP público do Balanceador de carga. Para obter detalhes, consulte [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Selecione um, ou não as portas sob **Selecione as portas de entrada públicas**. Portal 3389 está selecionada, para ativar o acesso remoto para a máquina virtual do Windows Server a partir da internet. Abrir a porta 3389 da internet não é recomendado para cargas de trabalho de produção.

   ![Selecione uma porta](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Aceite as predefinições restantes e selecione **OK**.
8. Na página **Resumo**, selecione **Criar**. A máquina virtual demora alguns minutos a implementar.
9. Depois de implementada a máquina virtual, introduza *myPublicIpAddress* na caixa de pesquisa na parte superior do portal. Quando **myPublicIpAddress** aparecer nos resultados da pesquisa, selecione.
10. Pode ver o endereço IP público, que é atribuído, e o endereço atribuído para o **myVM** máquina virtual, conforme mostrado na imagem seguinte:

    ![Endereço IP público do Vista](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure atribuído um endereço IP público a partir de endereços utilizados na região que criou a máquina virtual numa. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

11. Selecione **Configuration** para confirmar que a atribuição está **estático**.

    ![Endereço IP público do Vista](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Não modifique as definições do endereço IP no sistema de operativo da máquina virtual. Desconhece o sistema operativo de endereços IP públicos do Azure. Embora possa adicionar definições de endereço IP privadas para o sistema operativo, recomendamos que não se o fizer, a menos que necessário e não até depois de ler [adicionar um endereço IP privado para um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure
- Saiba mais sobre todas as [definições do endereço IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e a atribuição de um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) para uma máquina virtual do Azure
- Saiba mais sobre a criação [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais