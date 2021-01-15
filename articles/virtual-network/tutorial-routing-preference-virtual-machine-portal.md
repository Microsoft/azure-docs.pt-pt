---
title: Configure preferência de encaminhamento para um VM - Portal Azure
description: Saiba como criar um VM com um endereço IP público com escolha de preferência de encaminhamento utilizando o portal Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: beb670106690645a80aa12b930e3a005efb4282a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221651"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Configure a preferência de encaminhamento para um VM utilizando o portal Azure

Este artigo mostra-lhe como configurar a preferência de encaminhamento para uma máquina virtual. O tráfego ligado à Internet a partir do VM será encaminhado através da rede ISP quando escolher a **Internet** como a sua opção de preferência de encaminhamento . O encaminhamento padrão é através da rede global da Microsoft.

Este artigo mostra-lhe como criar uma máquina virtual com um IP público que está definido para encaminhar o tráfego através da internet pública através do portal Azure.

> [!IMPORTANT]
> A preferência de encaminhamento está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://preview.portal.azure.com/).

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
    |A localização| Selecione **E.U.A. Leste**.|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. No **separador 'Rede',** clique em **Criar novo** endereço IP **público.**
6. Insira *myPublicIpAddress*, selecione sku como **Standard**, e, em seguida, selecione a preferência de encaminhamento **Internet** e, em seguida, bata **ok**, como mostrado na seguinte imagem:

   ![Selecione estática](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Selecione uma porta ou não há portas em **portas de entrada públicas Selecionadas.** O Portal 3389 encontra-se selecionado para permitir o acesso remoto à máquina virtual do Windows Server a partir da internet. A abertura da porta 3389 da internet não é recomendada para cargas de trabalho de produção.

   ![Selecione uma porta](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Aceite as definições predefinitivas restantes e selecione **OK**.
8. Na página **Resumo**, selecione **Criar**. A máquina virtual demora alguns minutos a ser implantada.
9. Assim que a máquina virtual for implantada, insira o *myPublicIpAddress* na caixa de pesquisa no topo do portal. Quando **o meuPublicIpAddress** aparecer nos resultados da pesquisa, selecione-o.
10. Pode visualizar o endereço IP público que é atribuído e que o endereço é atribuído à máquina virtual **myVM,** como mostra a seguinte imagem:

    ![A screenshot mostra o NIC Public I P para a interface de interface de rede mynic.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Selecione **Networking**, em seguida, clique em nic **minica** e, em seguida, selecione o endereço ip público para confirmar que a preferência de encaminhamento é atribuída como **Internet**.

    ![A screenshot mostra o endereço I P e a preferência de encaminhamento para um endereço Público I P.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [IP público com preferência por encaminhamento](routing-preference-overview.md).
- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).