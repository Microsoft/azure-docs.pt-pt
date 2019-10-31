---
title: Tutorial – configurar a rede virtual para Azure AD Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar e configurar uma sub-rede de rede virtual do Azure ou um emparelhamento de rede para uma instância de Azure Active Directory Domain Services usando o portal do Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 26122278ad74fb1d383ca7a900810b6060ee78f5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172620"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Tutorial: configurar a rede virtual para uma instância de Azure Active Directory Domain Services

Para fornecer conectividade a usuários e aplicativos, um domínio gerenciado Azure Active Directory Domain Services (Azure AD DS) é implantado em uma sub-rede de rede virtual do Azure. Essa sub-rede de rede virtual só deve ser usada para os recursos de domínio gerenciados fornecidos pela plataforma Azure. À medida que você cria suas próprias VMs e aplicativos, eles não devem ser implantados na mesma sub-rede de rede virtual. Em vez disso, você deve criar e implantar seus aplicativos em uma sub-rede de rede virtual separada ou em uma rede virtual separada que esteja emparelhada com a rede virtual do Azure AD DS.

Este tutorial mostra como criar e configurar uma sub-rede de rede virtual dedicada ou como emparelhar uma rede diferente para a rede virtual do domínio gerenciado AD DS do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Entender as opções de conectividade de rede virtual para recursos ingressados no domínio no Azure AD DS
> * Criar um intervalo de endereços IP e uma sub-rede adicional na rede virtual AD DS do Azure
> * Configurar o emparelhamento de rede virtual para uma rede separada do Azure AD DS

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Você precisa de privilégios de *administrador global* em seu locatário do Azure ad para habilitar o Azure AD DS.
* Você precisa de privilégios de *colaborador* em sua assinatura do Azure para criar os recursos de AD DS do Azure necessários.
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, você criará e configurará a instância de AD DS do Azure usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Opções de conectividade de carga de trabalho do aplicativo

No tutorial anterior, foi criado um domínio gerenciado do Azure AD DS que usava algumas opções de configuração padrão para a rede virtual. Essas opções padrão criaram uma rede virtual do Azure e uma sub-rede de rede virtual. Os controladores de domínio do Azure AD DS que fornecem os serviços de domínio gerenciado estão conectados a essa sub-rede de rede virtual.

Quando você cria e executa VMs que precisam usar o domínio gerenciado AD DS do Azure, a conectividade de rede precisa ser fornecida. Essa conectividade de rede pode ser fornecida de uma das seguintes maneiras:

* Crie uma sub-rede de rede virtual adicional na rede virtual padrão do Azure AD DS domínio gerenciado. Essa sub-rede adicional é onde você cria e conecta suas VMs.
    * Como as VMs fazem parte da mesma rede virtual, elas podem executar automaticamente a resolução de nomes e se comunicar com os controladores de domínio do Azure AD DS.
* Configure o emparelhamento de rede virtual do Azure da rede virtual do domínio gerenciado AD DS do Azure para uma ou mais redes virtuais separadas. Essas redes virtuais separadas são onde você cria e conecta suas VMs.
    * Ao configurar o emparelhamento de rede virtual, você também deve definir as configurações de DNS para usar a resolução de nomes de volta para os controladores de domínio do Azure AD DS.

Normalmente, você usa apenas uma dessas opções de conectividade de rede. A escolha quase sempre é como você deseja gerenciar os recursos do Azure separados. Se você quiser gerenciar AD DS do Azure e VMs conectadas como um grupo de recursos, poderá criar uma sub-rede de rede virtual adicional para VMs. Se você quiser separar o gerenciamento de AD DS do Azure e, em seguida, qualquer VM conectada, poderá usar o emparelhamento de rede virtual. Você também pode optar por usar o emparelhamento de rede virtual para fornecer conectividade a VMs existentes em seu ambiente do Azure que estão conectados a uma rede virtual existente.

Neste tutorial, você só precisa configurar uma dessas opções de conectividade de rede virtual.

Para obter mais informações sobre como planejar e configurar a rede virtual, consulte [considerações de rede para Azure Active Directory Domain Services] [Network-Considerations].

## <a name="create-a-virtual-network-subnet"></a>Criar uma sub-rede de rede virtual

Por padrão, a rede virtual do Azure criada com o domínio gerenciado AD DS do Azure contém uma única sub-rede de rede virtual. Essa sub-rede de rede virtual só deve ser usada pela plataforma Azure para fornecer serviços de domínio gerenciado. Para criar e usar suas próprias VMs nessa rede virtual do Azure, crie uma sub-rede adicional.

Para criar uma sub-rede de rede virtual para VMs e cargas de trabalho de aplicativo, conclua as seguintes etapas:

1. No portal do Azure, selecione o grupo de recursos do seu domínio gerenciado do Azure AD DS, como *MyResource*Group. Na lista de recursos, escolha a rede virtual padrão, como *aadds-vnet*.
1. No menu à esquerda da janela rede virtual, selecione **espaço de endereço**. A rede virtual é criada com um único espaço de endereço de *10.0.1.0/24*, que é usado pela sub-rede padrão.

    Adicione um intervalo de endereços IP adicional à rede virtual. O tamanho desse intervalo de endereços e o intervalo de endereços IP real a ser usado dependem de outros recursos de rede já implantados. O intervalo de endereços IP não deve se sobrepor a quaisquer intervalos de endereços existentes em seu ambiente do Azure ou local. Certifique-se de dimensionar o intervalo de endereços IP grande o suficiente para o número de VMs que você espera implantar na sub-rede.

    No exemplo a seguir, um intervalo de endereços IP adicional de *10.0.2.0/24* é adicionado. Quando estiver pronto, selecione **salvar**.

    ![Adicionar um intervalo de endereços IP de rede virtual adicional no portal do Azure](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Em seguida, no menu à esquerda da janela rede virtual, selecione sub- **redes**e, em seguida, escolha **+ sub** -rede para adicionar uma sub-rede.
1. Insira um nome para a sub-rede, como *cargas de trabalho*. Se necessário, atualize o **intervalo de endereços** se desejar usar um subconjunto do intervalo de endereços IP configurado para a rede virtual nas etapas anteriores. Por enquanto, deixe os padrões para opções como grupo de segurança de rede, tabela de rotas, pontos de extremidade de serviço.

    No exemplo a seguir, é criada uma sub-rede chamada *cargas de trabalho* que usa o intervalo de endereços IP *10.0.2.0/24* :

    ![Adicionar uma sub-rede de rede virtual adicional no portal do Azure](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Quando estiver pronto, selecione **OK**. Demora alguns minutos para criar a sub-rede da rede virtual.

Quando você cria uma VM que precisa usar o domínio gerenciado AD DS do Azure, certifique-se de selecionar esta sub-rede de rede virtual. Não crie VMs no padrão *aadds-subnet*. Se você selecionar uma rede virtual diferente, não haverá conectividade de rede e resolução de DNS para acessar o domínio gerenciado AD DS do Azure, a menos que você configure o emparelhamento de rede virtual.

## <a name="configure-virtual-network-peering"></a>Configurar o emparelhamento de rede virtual

Você pode ter uma rede virtual do Azure existente para VMs ou deseja manter a rede virtual de domínio gerenciado AD DS do Azure separada. Para usar o domínio gerenciado, as VMs em outras redes virtuais precisam de uma maneira de se comunicar com os controladores de domínio do Azure AD DS. Essa conectividade pode ser fornecida usando o emparelhamento de rede virtual do Azure.

Com o emparelhamento de rede virtual do Azure, duas redes virtuais são conectadas, sem a necessidade de um dispositivo de VPN (rede virtual privada). O emparelhamento de rede permite que você conecte rapidamente redes virtuais e defina fluxos de tráfego em seu ambiente do Azure. Para obter mais informações sobre emparelhamento, consulte [visão geral de emparelhamento de rede virtual do Azure][peering-overview].

Para emparelhar uma rede virtual com a rede virtual de domínio gerenciado AD DS do Azure, conclua as seguintes etapas:

1. Escolha a rede virtual padrão criada para sua instância de AD DS do Azure chamada *aadds-vnet*.
1. No menu à esquerda da janela rede virtual, selecione **emparelhamentos**.
1. Para criar um emparelhamento, selecione **+ Adicionar**. No exemplo a seguir, o *aadds-vnet* padrão é emparelhado com uma rede virtual chamada *myVnet*. Defina as seguintes configurações com seus próprios valores:

    * **Nome do emparelhamento de aadds-vnet para rede virtual remota**: um identificador descritivo das duas redes, como *aadds-vnet-to-myvnet*
    * **Tipo de implantação de rede virtual**: *Gerenciador de recursos*
    * **Assinatura**: a assinatura da rede virtual à qual você deseja emparelhar, como o *Azure*
    * **Rede virtual**: a rede virtual à qual você deseja emparelhar, como *myVnet*
    * **Nome do emparelhamento de myVnet para aadds-vnet**: um identificador descritivo das duas redes, como *myVnet-to-aadds-vnet*

    ![Configurar o emparelhamento de rede virtual no portal do Azure](./media/tutorial-configure-networking/create-peering.png)

    Deixe quaisquer outros padrões de acesso à rede virtual ou tráfego encaminhado, a menos que você tenha requisitos específicos para seu ambiente e selecione **OK**.

1. Demora alguns minutos para criar o emparelhamento na rede virtual AD DS do Azure e na rede virtual selecionada. Quando estiver pronto, os relatórios de **status de emparelhamento** *conectados*, conforme mostrado no exemplo a seguir:

    ![Redes emparelhadas conectadas com êxito no portal do Azure](./media/tutorial-configure-networking/connected-peering.png)

Antes que as VMs na rede virtual emparelhada possam usar o domínio gerenciado AD DS do Azure, configure os servidores DNS para permitir a resolução correta de nomes.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Configurar servidores DNS na rede virtual emparelhada

Para VMs e aplicativos na rede virtual emparelhada para se comunicar com êxito com o domínio gerenciado do Azure AD DS, as configurações de DNS devem ser atualizadas. Os endereços IP dos controladores de domínio do Azure AD DS devem ser configurados como os servidores DNS na rede virtual emparelhada. Há duas maneiras de configurar os controladores de domínio como servidores DNS para a rede virtual emparelhada:

* Configure os servidores DNS da rede virtual do Azure para usar os controladores de domínio do Azure AD DS.
* Configure o servidor DNS existente em uso na rede virtual emparelhada para usar o encaminhamento DNS condicional para direcionar consultas para o domínio gerenciado AD DS do Azure. Essas etapas variam dependendo do servidor DNS existente em uso.

Neste tutorial, vamos configurar os servidores DNS da rede virtual do Azure para direcionar todas as consultas para os controladores de domínio do Azure AD DS.

1. No portal do Azure, selecione o grupo de recursos da rede virtual emparelhada, como *MyResource*Group. Na lista de recursos, escolha a rede virtual emparelhada, como *myVnet*.
1. No menu à esquerda da janela rede virtual, selecione **servidores DNS**.
1. Por padrão, uma rede virtual usa os servidores DNS internos fornecidos pelo Azure. Escolha usar servidores DNS **personalizados** . Insira os endereços IP para os controladores de domínio AD DS do Azure, que geralmente são *10.0.1.4* e *10.0.1.5*. Confirme esses endereços IP na janela **visão geral** do seu domínio gerenciado AD DS do Azure no Portal.

    ![Configurar os servidores DNS da rede virtual para usar os controladores de domínio do Azure AD DS](./media/tutorial-configure-networking/custom-dns.png)

1. Quando estiver pronto, selecione **salvar**. Leva alguns minutos para atualizar os servidores DNS para a rede virtual.
1. Para aplicar as configurações DNS atualizadas às VMs, reinicie as VMs conectadas à rede virtual emparelhada.

Quando você cria uma VM que precisa usar o domínio gerenciado AD DS do Azure, certifique-se de selecionar esta rede virtual emparelhada. Se você selecionar uma rede virtual diferente, não haverá conectividade de rede e resolução de DNS para acessar o domínio gerenciado AD DS do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Entender as opções de conectividade de rede virtual para recursos ingressados no domínio no Azure AD DS
> * Criar um intervalo de endereços IP e uma sub-rede adicional na rede virtual AD DS do Azure
> * Configurar o emparelhamento de rede virtual para uma rede separada do Azure AD DS

Para ver esse domínio gerenciado em ação, crie e ingresse uma máquina virtual no domínio.

> [!div class="nextstepaction"]
> [Ingressar uma máquina virtual do Windows Server em seu domínio gerenciado](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
