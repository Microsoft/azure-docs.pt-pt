---
title: Ligue-se a uma rede de pares em Azure Lab Services | Microsoft Docs
description: Saiba como ligar a sua rede de laboratório a outra rede como um par. Por exemplo, ligue a sua organização/rede universitária no local com a rede virtual do Lab em Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 99ea72f11c8f389a15171a1fe3b376646494903a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434248"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Ligue a rede do seu laboratório a uma rede virtual de pares nos Serviços Azure Lab

Este artigo fornece informações sobre olhando a sua rede de laboratórios com outra rede.

## <a name="overview"></a>Descrição geral

O espreitamento de rede virtual permite-lhe ligar sem problemas as redes virtuais do Azure. Uma vez executado o peering, as redes virtuais aparecem como uma única, para fins de conectividade. O tráfego entre máquinas virtuais nas redes virtuais é encaminhado através da infraestrutura da Espinha Dorsal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual, apenas através de endereços IP privados. Para obter mais informações, consulte [a rede Virtual a espreitar.](../virtual-network/virtual-network-peering-overview.md)

Poderá ter de ligar a rede do seu laboratório a uma rede virtual de pares em alguns cenários, incluindo os seguintes:

- As máquinas virtuais do laboratório têm software que se conecta a servidores de licença no local para adquirir licença.
- As máquinas virtuais do laboratório precisam de acesso a conjuntos de dados (ou quaisquer outros ficheiros) nas partilhas de rede da universidade.

Algumas redes no local estão ligadas à Rede Virtual Azure, através do [ExpressRoute](../expressroute/expressroute-introduction.md) ou [do Virtual Network Gateway.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Estes serviços devem ser criados fora dos Serviços de Laboratório Azure. Para saber mais sobre a ligação de uma rede no local ao Azure utilizando o ExpressRoute, consulte a [visão geral do ExpressRoute](../expressroute/expressroute-introduction.md). Para a conectividade no local usando um Gateway de rede virtual, o gateway, rede virtual especificada, e a conta de laboratório devem estar todos na mesma região.

> [!NOTE]
> Ao criar uma Rede Virtual Azure que será espreitada com uma conta de laboratório, é importante entender como a região da rede virtual impacta onde os laboratórios são criados.  Para mais informações, consulte a secção do guia do administrador sobre [as regiões\localizações](./administrator-guide.md#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação de conta de laboratório

Durante a nova [criação de conta de laboratório,](tutorial-setup-lab-account.md)pode escolher uma rede virtual existente que apareça na lista de dropdown **da rede virtual Peer** no separador **Advanced.**  A lista só mostrará redes virtuais na mesma região que a conta de laboratório. A rede virtual selecionada está ligada (espreitada) a laboratórios criados sob a conta de laboratório.  Todas as máquinas virtuais em laboratórios que são criadas após a realização desta alteração terão acesso aos recursos da rede virtual.

![Selecione VNet para peer](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Intervalo de endereços

Existe também uma opção para fornecer **a gama de endereços** para máquinas virtuais para os laboratórios.  A propriedade **do intervalo de endereços** só se aplica se uma **rede virtual de pares** estiver ativada para o laboratório. Se o intervalo de endereços for fornecido, todas as máquinas virtuais nos laboratórios sob a conta de laboratório serão criadas nesse intervalo de endereços. O intervalo de endereços deve estar na notação CIDR (por exemplo, 10.20.0.0/20) e não se sobreponha a quaisquer intervalos de endereços existentes.  Ao fornecer um intervalo de *endereços,* é importante pensar no número de laboratórios que serão criados e fornecer um intervalo de endereços para acomodar isso. Os Serviços de Laboratório assumem um máximo de 512 máquinas virtuais por laboratório.  Por exemplo, uma gama ip com '/23' pode criar apenas um laboratório.  Uma gama com um '/21' permitirá a criação de quatro laboratórios.

Se a **gama Address** não for especificada, os Serviços de Laboratório utilizarão o intervalo de endereços predefinido que lhe foi dado pela Azure ao criar a rede virtual para ser espreitada pela sua rede virtual.  O alcance é frequentemente algo como 10.x.0.0/16.  Isto pode levar à sobreposição do intervalo ip, por isso certifique-se de especificar um intervalo de endereços nas definições do laboratório ou verificar o intervalo de endereços da sua rede virtual a ser espreitada.

> [!NOTE]
> A criação de laboratório pode falhar se a conta do laboratório for espreitada para uma rede virtual, mas tem um alcance de endereço IP demasiado estreito. Pode ficar sem espaço na gama de endereços se houver demasiados laboratórios na conta de laboratório (cada laboratório usa 512 endereços). 
> 
> Se a criação do laboratório falhar, contacte o proprietário/administrador da sua conta de laboratório e solicite que o intervalo de endereços seja aumentado. O administrador pode aumentar a gama de endereços usando as etapas mencionadas na [Especificação de um intervalo de endereços para VMs numa](#specify-an-address-range-for-vms-in-the-lab-account) secção de conta de laboratório. 

## <a name="configure-after-the-lab-account-is-created"></a>Configurar após a criação da conta de laboratório

A mesma propriedade pode ser ativada a partir do separador de **configuração labs** da página **de Conta de Laboratório** se não configurar uma rede de pares no momento da criação de conta de laboratório. A alteração feita a esta definição aplica-se apenas aos laboratórios que são criados após a alteração. Como pode ver na imagem, pode ativar ou desativar a **rede virtual peer** para laboratórios na conta de laboratório.

![Ativar ou desativar o olho vnet após a criação do laboratório](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Quando seleciona uma rede virtual para o campo **de rede virtual Peer,** o criador do laboratório allow para escolher a opção **de localização do laboratório** é desativado. Isso porque os laboratórios na conta do laboratório devem estar na mesma região que o laboratório para que se conectem com recursos na rede virtual de pares.

> [!IMPORTANT]
> A definição de rede virtual espreitada aplica-se apenas aos laboratórios que são criados após a alteração, e não aos laboratórios existentes.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>Especificar um intervalo de endereços para VMs na conta de laboratório
O procedimento a seguir tem passos para especificar um intervalo de endereços para VMs no laboratório. Se atualizar o intervalo que especificou anteriormente, o intervalo de endereços modificado aplica-se apenas aos VM que são criados após a alteração. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que deve ter em mente. 

- O prefixo deve ser menor ou igual a 23. 
- Se uma rede virtual for espreitada para a conta de laboratório, o intervalo de endereços fornecido não pode sobrepor-se com a gama de endereços a partir de rede virtual espreitada.

1. Na página **'Contas de Laboratório',** selecione **as definições de Laboratório** no menu esquerdo.
2. Para o campo **de intervalo de endereços,** especifique o intervalo de endereços para VMs que serão criados no laboratório. O intervalo de endereços deve estar na notação de encaminhamento inter-domínio (CIDR) sem classe (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas nesta gama de endereços.
3. Selecione **Guardar** na barra de ferramentas. 

    ![Gama de endereços configurar](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Anexar uma galeria de imagens partilhada a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um utilizador como dono de laboratório](how-to-add-user-lab-owner.md)
- [Ver definições de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras configurações para um laboratório](how-to-configure-lab-accounts.md)