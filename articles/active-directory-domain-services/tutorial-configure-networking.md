---
title: Tutorial - Configure rede virtual para Serviços de Domínio Azure AD [ Microsoft Docs
description: Neste tutorial, aprende-se a criar e configurar uma subnet de rede virtual Azure ou a procurar uma instância de Serviços de Domínio de Diretório Ativo Azure utilizando o portal Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: af284e4c10487123c8c2a2105a25a2285ae0aa99
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474394"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Tutorial: Configure a rede virtual para uma instância de Serviços de Domínio de Diretório Ativo Azure

Para fornecer conectividade aos utilizadores e aplicações, um domínio gerido pelo Azure Ative Directory Domain Services (Azure AD DS) é implantado numa subnet de rede virtual Azure. Esta subrede de rede virtual só deve ser utilizada para os recursos de domínio geridos fornecidos pela plataforma Azure. À medida que crias os teus próprios VMs e aplicações, elas não devem ser implantadas na mesma subnet de rede virtual. Em vez disso, deve criar e implementar as suas aplicações numa subnet de rede virtual separada, ou numa rede virtual separada que seja observada para a rede virtual Azure AD DS.

Este tutorial mostra-lhe como criar e configurar uma rede virtual dedicada ou como fazer um peer a diferente rede para a rede virtual gerida pelo Azure AD DS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Compreenda as opções de conectividade da rede virtual para recursos associados ao Azure AD DS
> * Criar uma gama de endereços IP e uma subrede adicional na rede virtual Azure AD DS
> * Configure rede virtual olhando para uma rede separada do Azure AD DS

Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Você precisa de privilégios *de administrador global* no seu inquilino Azure AD para permitir o Azure AD DS.
* Você precisa de privilégios *Contributivos* na sua subscrição Azure para criar os recursos DS Azure necessários.
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, o primeiro tutorial cria e configura uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria e configura a instância Azure AD DS utilizando o portal Azure. Para começar, inicie a inscrição no [portal Azure.](https://portal.azure.com)

## <a name="application-workload-connectivity-options"></a>Opções de conectividade da carga de trabalho de aplicação

No tutorial anterior, foi criado um domínio gerido por Azure AD DS que utilizou algumas opções de configuração padrão para a rede virtual. Estas opções predefinidas criaram uma rede virtual Azure e uma subnet de rede virtual. Os controladores de domínio Azure AD DS que fornecem os serviços de domínio geridos estão ligados a esta subnet de rede virtual.

Quando cria e executa VMs que precisam de utilizar o domínio gerido pelo Azure AD DS, a conectividade da rede tem de ser fornecida. Esta conectividade da rede pode ser fornecida de uma das seguintes formas:

* Crie uma subrede de rede virtual adicional na rede virtual gerida por Azure AD DS. Esta sub-rede adicional é onde cria e liga os seus VMs.
    * Como os VMs fazem parte da mesma rede virtual, podem executar automaticamente a resolução de nomes e comunicar com os controladores de domínio Azure AD DS.
* Configure A rede virtual Azure espreitando do Azure AD DS gerido a rede virtual do domínio para uma ou mais redes virtuais separadas. Estas redes virtuais separadas são onde cria e conecta os seus VMs.
    * Quando configurar o epeering de rede virtual, o seu deve também configurar as definições de DNS para utilizar a resolução de nomes de volta para os controladores de domínio Azure AD DS.

Normalmente, utilize apenas uma destas opções de conectividade de rede. A escolha resume-se muitas vezes à forma como pretende gerir os seus recursos Azure. Se quiser gerir o Azure AD DS e os VMs conectados como um grupo de recursos, pode criar uma subrede de rede virtual adicional para VMs. Se pretender separar a gestão do Azure AD DS e, em seguida, quaisquer VMs conectados, pode utilizar o peering de rede virtual. Também pode optar por utilizar o peering de rede virtual para fornecer conectividade aos VMs existentes no seu ambiente Azure que estão ligados a uma rede virtual existente.

Neste tutorial, basta configurar uma destas opções de conectividade de rede virtual.

Para obter mais informações sobre como planear e configurar a rede virtual, consulte [considerações de networking para o Azure Ative Directory Domain Services][considerações de rede].

## <a name="create-a-virtual-network-subnet"></a>Criar uma subrede de rede virtual

Por padrão, a rede virtual Azure criada com o domínio gerido pelo Azure AD DS contém uma única subnet de rede virtual. Esta subnet de rede virtual só deve ser utilizada pela plataforma Azure para fornecer serviços de domínio geridos. Para criar e utilizar os seus próprios VMs nesta rede virtual Azure, crie uma subrede adicional.

Para criar uma subrede de rede virtual para VMs e cargas de trabalho de aplicação, complete os seguintes passos:

1. No portal Azure, selecione o grupo de recursos do seu domínio gerido azure AD DS, como o *myResourceGroup*. A partir da lista de recursos, escolha a rede virtual padrão, como *aadds-vnet*.
1. No menu à esquerda da janela de rede virtual, selecione **espaço Address**. A rede virtual é criada com um único espaço de endereço de *10.0.2.0/24,* que é utilizado pela sub-rede predefinida.

    Adicione uma gama adicional de endereços IP à rede virtual. A dimensão desta gama de endereços e a gama real de endereços IP a utilizar depende de outros recursos de rede já implantados. A gama de endereços IP não deve sobrepor-se a quaisquer gamas de endereços existentes no seu ambiente Azure ou no local. Certifique-se de que dimensiona a gama de endereços IP suficientemente grande para o número de VMs que espera implantar na sub-rede.

    No exemplo seguinte, é adicionada uma gama adicional de endereços IP de *10.0.3.0/24.* Quando estiver pronto, selecione **Guardar**.

    ![Adicione uma gama adicional de endereços IP da rede virtual no portal Azure](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Em seguida, no menu esquerdo da janela de rede virtual, selecione **Subnets,** em seguida, escolha **+ Subnet** para adicionar uma subnet.
1. Introduza um nome para a sub-rede, como *cargas*de trabalho . Se necessário, atualize a **gama 'Endereço'** se pretender utilizar um subconjunto da gama de endereços IP configurado para a rede virtual nos passos anteriores. Por enquanto, deixe os predefinidos para opções como grupo de segurança de rede, tabela de rotas, pontos finais de serviço.

    No exemplo seguinte, é criada uma subnet chamada *cargas* de trabalho que utiliza a gama de endereços IP *10.0.3.0/24:*

    ![Adicione uma subrede de rede virtual adicional no portal Azure](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Quando estiver pronto, selecione **OK**. Leva alguns momentos para criar a rede virtual subnet.

Quando criar um VM que precisa de utilizar o domínio gerido pelo Azure AD DS, certifique-se de selecionar esta sub-rede virtual. Não crie VMs na *rede aadds-subnet*padrão . Se selecionar uma rede virtual diferente, não existe conectividade de rede e resolução dNS para chegar ao domínio gerido pelo Azure AD DS a menos que configure o peering virtual da rede.

## <a name="configure-virtual-network-peering"></a>Configurar o peering da rede virtual

Pode ter uma rede virtual Azure existente para VMs, ou desejar manter separada a sua rede virtual de domínio gerida pelo Azure AD DS. Para utilizar o domínio gerido, os VMs noutras redes virtuais precisam de uma forma de comunicar com os controladores de domínio Azure AD DS. Esta conectividade pode ser fornecida utilizando o peering da rede virtual Azure.

Com o peering da rede virtual Azure, duas redes virtuais estão conectadas, sem a necessidade de um dispositivo de rede privada virtual (VPN). O peering da rede permite ligar rapidamente redes virtuais e definir fluxos de tráfego em todo o seu ambiente Azure. Para obter mais informações sobre o peering, consulte a visão geral da [rede virtual Azure][peering-overview].

Para espreitar uma rede virtual para a rede virtual de domínio gerida pelo Azure AD DS, complete os seguintes passos:

1. Escolha a rede virtual predefinida criada para a sua instância Azure AD DS chamada *aadds-vnet*.
1. No menu à esquerda da janela de rede virtual, selecione **Peerings**.
1. Para criar um epeering, selecione **+ Adicionar**. No exemplo seguinte, o *aadds-vnet* padrão é analisado para uma rede virtual chamada *myVnet*. Configure as seguintes definições com os seus próprios valores:

    * **Nome do peering de aadds-vnet para rede virtual remota**: Um identificador descritivo das duas redes, tais como *aadds-vnet-to-myvnet*
    * **Tipo de implementação de rede virtual**: *Gestor de Recursos*
    * **Subscrição**: A subscrição da rede virtual a que pretende conhecer, como o *Azure*
    * **Rede virtual**: A rede virtual a que pretende saquear, como o *myVnet*
    * **Nome do peering de myVnet para aadds-vnet**: Um identificador descritivo das duas redes, tais como *myvnet-to-aadds-vnet*

    ![Configure o olhar da rede virtual no portal Azure](./media/tutorial-configure-networking/create-peering.png)

    Deixe quaisquer outros incumprimentos para acesso virtual à rede ou tráfego reencaminhado a menos que tenha requisitos específicos para o seu ambiente, em seguida, selecione **OK**.

1. São precisos alguns momentos para criar o olhar na rede virtual Azure AD DS e na rede virtual que selecionou. Quando estiver pronto, o **estado de peering** *reporta Ligados,* como mostra o seguinte exemplo:

    ![Redes com sucesso conectadas no portal Azure](./media/tutorial-configure-networking/connected-peering.png)

Antes de os VMs na rede virtual peered poderem utilizar o domínio gerido pelo Azure AD DS, configure os servidores DNS para permitir uma resolução correta de nomes.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Configure os servidores DNS na rede virtual peered

Para que os VMs e aplicações na rede virtual peered falem com sucesso com o domínio gerido pelo Azure AD DS, as definições de DNS devem ser atualizadas. Os endereços IP dos controladores de domínio Azure AD DS devem ser configurados como os servidores DNS na rede virtual peered. Existem duas formas de configurar os controladores de domínio como servidores DNS para a rede virtual peered:

* Configure os servidores DNS da rede virtual Azure para utilizar os controladores de domínio Azure AD DS.
* Configure o servidor DNS existente em uso na rede virtual peered para utilizar DNS condicionados reencaminhamento para consultas diretas para o domínio gerido pelo Azure AD DS. Estes passos variam consoante o servidor DNS existente em uso.

Neste tutorial, vamos configurar os servidores DNS da rede virtual Azure para direcionar todas as consultas para os controladores de domínio Azure AD DS.

1. No portal Azure, selecione o grupo de recursos da rede virtual peered, como o *myResourceGroup*. A partir da lista de recursos, escolha a rede virtual peered, como *myVnet*.
1. No menu à esquerda da janela de rede virtual, selecione **servidores DNS**.
1. Por padrão, uma rede virtual utiliza os servidores DNS fornecidos pelo Azure. Opte por utilizar servidores DNS **personalizados.** Introduza os endereços IP dos controladores de domínio Azure AD DS, que são normalmente *10.0.2.4* e *10.0.2.5*. Confirme estes endereços IP na janela **de visão geral** do seu domínio gerido azure AD DS no portal.

    ![Configure os servidores DNS da rede virtual para utilizar os controladores de domínio Azure AD DS](./media/tutorial-configure-networking/custom-dns.png)

1. Quando estiver pronto, selecione **Guardar**. São precisos alguns momentos para atualizar os servidores DNS para a rede virtual.
1. Para aplicar as definições dNS atualizadas aos VMs, reinicie os VMs ligados à rede virtual peered.

Quando criar um VM que precisa de utilizar o domínio gerido pelo Azure AD DS, certifique-se de selecionar esta rede virtual com paro. Se selecionar uma rede virtual diferente, não existe conectividade de rede e resolução DNS para chegar ao domínio gerido pelo Azure AD DS.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Compreenda as opções de conectividade da rede virtual para recursos associados ao Azure AD DS
> * Criar uma gama de endereços IP e uma subrede adicional na rede virtual Azure AD DS
> * Configure rede virtual olhando para uma rede separada do Azure AD DS

Para ver este domínio gerido em ação, crie e junte uma máquina virtual ao domínio.

> [!div class="nextstepaction"]
> [Junte-se a uma máquina virtual do Windows Server ao seu domínio gerido](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
