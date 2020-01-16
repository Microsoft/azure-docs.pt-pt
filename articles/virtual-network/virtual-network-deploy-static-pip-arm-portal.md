---
title: Criar uma VM com um endereço IP público estático-portal do Azure | Microsoft Docs
description: Saiba como criar uma VM com um endereço IP público estático usando o portal do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 66050c16f40e0a06117327ef53e3aae87d03c5db
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043536"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Criar uma máquina virtual com um endereço IP público estático usando o portal do Azure

Você pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite que você se comunique com uma máquina virtual da Internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca seja alterado. Saiba mais sobre [endereços IP públicos estáticos](virtual-network-ip-addresses-overview-arm.md#allocation-method). Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmico para estático ou para trabalhar com endereços IP privados, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Os endereços IP públicos têm um [encargo nominal](https://azure.microsoft.com/pricing/details/ip-addresses)e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por assinatura.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do portal do Azure.
2. Selecione **computação**e, em seguida, selecione **VM do Windows Server 2016**ou outro sistema operacional de sua escolha.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Nome|myVM|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Utilizar existente** e selecione **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**.|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. Em **configurações**, selecione **endereço IP público**.
6. Insira *myPublicIpAddress*, selecione **estático**e, em seguida, selecione **OK**, conforme mostrado na figura a seguir:

   ![Selecionar estático](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Se o endereço IP público precisar ser um SKU padrão, selecione **Standard** em **SKU**. Saiba mais sobre as [SKUs de endereço IP público](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual for adicionada ao pool de back-end de um Azure Load Balancer público, a SKU do endereço IP público da máquina virtual deverá corresponder à SKU do endereço IP público do balanceador de carga. Para obter detalhes, consulte [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

6. Selecione uma porta ou não portas em **selecionar portas de entrada públicas**. O portal 3389 está selecionado para habilitar o acesso remoto à máquina virtual do Windows Server pela Internet. A abertura da porta 3389 da Internet não é recomendada para cargas de trabalho de produção.

   ![Selecionar uma porta](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Aceite as configurações padrão restantes e selecione **OK**.
8. Na página **Resumo**, selecione **Criar**. A máquina virtual leva alguns minutos para ser implantada.
9. Depois que a máquina virtual for implantada, insira *myPublicIpAddress* na caixa de pesquisa na parte superior do Portal. Quando **myPublicIpAddress** aparecer nos resultados da pesquisa, selecione-o.
10. Você pode exibir o endereço IP público que é atribuído e que o endereço é atribuído à máquina virtual **myVM** , conforme mostrado na figura a seguir:

    ![Exibir endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    O Azure atribuiu um endereço IP público dos endereços usados na região em que você criou a máquina virtual. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

11. Selecione **configuração** para confirmar que a atribuição é **estática**.

    ![Exibir endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Não modifique as configurações de endereço IP no sistema operacional da máquina virtual. O sistema operacional não reconhece os endereços IP públicos do Azure. Embora você possa adicionar configurações de endereço IP privado ao sistema operacional, é recomendável não fazer isso a menos que seja necessário, e não até depois de ler [Adicionar um endereço IP privado a um sistema operacional](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuindo um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual do Azure
- Saiba mais sobre como criar máquinas virtuais do [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e do [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)