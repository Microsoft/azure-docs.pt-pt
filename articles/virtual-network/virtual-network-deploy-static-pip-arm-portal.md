---
title: Criar um VM com um endereço IP público estático - portal Azure / Microsoft Docs
description: Aprenda a criar um VM com um endereço IP público estático utilizando o portal Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 0de28fc75d5eb1b0867e4ba6d8eda9f0f42c8498
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148008"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Crie uma máquina virtual com um endereço IP público estático utilizando o portal Azure

Pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite-lhe comunicar com uma máquina virtual a partir da internet. Atribuir um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca se altera. Saiba mais sobre [endereços IP públicos estáticos.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmica para estática, ou para trabalhar com endereços IP privados, ver [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Os endereços IP públicos têm uma [taxa nominal,](https://azure.microsoft.com/pricing/details/ip-addresses)e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por subscrição.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do portal do Azure.
2. Selecione **Compute**, e, em seguida, selecione **Windows Server 2016 VM**, ou outro sistema operativo à sua escolha.
3. Introduza, ou selecione, as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **OK:**

    |Definição|Valor|
    |---|---|
    |Nome|myVM|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Utilizar existente** e selecione **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**.|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. Em **Definições,** selecione **endereço IP público**.
6. Introduza *o myPublicIpAddress*, selecione **Static**, e, em seguida, selecione **OK**, como mostrado na seguinte imagem:

   ![Selecione estática](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Se o endereço IP público tiver de ser um SKU padrão, selecione **Standard** sob **SKU**. Saiba mais sobre o [endereço IP Público SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual for adicionada ao pool de back-end de um equilíbrio público de carga Azure, o SKU do endereço IP público da máquina virtual deve corresponder ao SKU do endereço IP público do equilibrador de carga. Para mais detalhes, consulte [O Equilíbrio de Carga Sinuoso Azure](../load-balancer/concepts-limitations.md#skus).

6. Selecione uma porta ou nenhuma porta sob **portas de entrada pública Selecione**. O Portal 3389 é selecionado, para permitir o acesso remoto à máquina virtual do Windows Server a partir da internet. A abertura da porta 3389 da internet não é recomendada para cargas horárias de produção.

   ![Selecione uma porta](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Aceite as definições predefinidas restantes e selecione **OK**.
8. Na página **Resumo**, selecione **Criar**. A máquina virtual leva alguns minutos para ser implantada.
9. Uma vez que a máquina virtual é implantada, introduza o *myPublicIpAddress* na caixa de pesquisa na parte superior do portal. Quando o **myPublicIpAddress** aparecer nos resultados da pesquisa, selecione-o.
10. Pode ver o endereço IP público que está atribuído e que o endereço é atribuído à máquina virtual **myVM,** como mostra a seguinte imagem:

    ![Ver endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    O Azure atribuiu um endereço IP público a partir de endereços utilizados na região onde criou a máquina virtual. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

11. **Selecione Configuração** para confirmar que a atribuição é **Estática**.

    ![Ver endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Não modifique as definições de endereço IP dentro do sistema operativo da máquina virtual. O sistema operativo desconhece os endereços IP públicos do Azure. Embora possa adicionar definições privadas de endereço IP ao sistema operativo, recomendamos que não o faça a menos que seja necessário, e não até depois de ler [Adicione um endereço IP privado a um sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure
- Saiba mais sobre todas as [definições de endereçoip público](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuindo um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual Azure
- Saiba mais sobre a criação de máquinas virtuais [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)