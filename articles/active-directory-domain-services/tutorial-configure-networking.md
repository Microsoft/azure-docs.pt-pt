---
title: Tutorial - Configurar rede virtual para Serviços de Domínio Azure AD / Microsoft Docs
description: Neste tutorial, aprende-se a criar e configurar uma sub-rede de rede virtual Azure ou uma rede que espreita um domínio gerido por um Azure Ative Directory Domain Services utilizando o portal Azure.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618421"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Configurar a rede virtual para um domínio gerido por Azure Ative Directory Domain Services

Para fornecer conectividade aos utilizadores e aplicações, um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS) é implantado numa sub-rede de rede virtual Azure. Esta sub-rede de rede virtual só deve ser utilizada para os recursos de domínio geridos fornecidos pela plataforma Azure.

Quando crias os teus próprios VMs e aplicações, elas não devem ser implantadas na mesma sub-rede de rede virtual. Em vez disso, deve criar e implementar as suas aplicações numa sub-rede de rede virtual separada, ou numa rede virtual separada que seja espreitada pela rede virtual Azure AD DS.

Este tutorial mostra-lhe como criar e configurar uma sub-rede de rede virtual dedicada ou como espreitar uma rede diferente para a rede virtual gerida pelo Azure AD DS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Compreenda as opções de conectividade de rede virtual para recursos unidos pelo domínio ao Azure AD DS
> * Criar uma gama de endereços IP e sub-redes adicionais na rede virtual Azure AD DS
> * Configure a rede virtual que espreita para uma rede separada do Azure AD DS

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Você precisa de privilégios *de administrador global* no seu inquilino AZure AD para configurar Azure AD DS.
* Precisa de privilégios *colaboradores* na sua subscrição Azure para criar os recursos Azure AD DS necessários.
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, o primeiro tutorial cria e configura um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria-se e configura o domínio gerido utilizando o portal Azure. Para começar, inicie primeiro o sinal no [portal Azure.](https://portal.azure.com)

## <a name="application-workload-connectivity-options"></a>Opções de conectividade de carga de trabalho de aplicação

No tutorial anterior, foi criado um domínio gerido que utilizou algumas opções de configuração padrão para a rede virtual. Estas opções padrão criaram uma rede virtual Azure e uma sub-rede de rede virtual. Os controladores de domínio Azure AD DS que fornecem os serviços de domínio geridos estão ligados a esta sub-rede de rede virtual.

Quando cria e executa VMs que precisam de utilizar o domínio gerido, a conectividade da rede precisa de ser fornecida. Esta conectividade de rede pode ser fornecida de uma das seguintes formas:

* Crie uma sub-rede de rede virtual adicional na rede virtual do domínio gerido. Esta sub-rede adicional é onde cria e liga os seus VMs.
    * Uma vez que os VMs fazem parte da mesma rede virtual, podem executar automaticamente a resolução de nomes e comunicar com os controladores de domínio Azure AD DS.
* Configurar a rede virtual Azure olhando da rede virtual do domínio gerido para uma ou mais redes virtuais separadas. Estas redes virtuais separadas são onde cria e conecta os seus VMs.
    * Quando configurar o espreitamento da rede virtual, também deve configurar as definições de DNS para utilizar a resolução de nomes de volta para os controladores de domínio Azure AD DS.

Normalmente, só se usa uma destas opções de conectividade de rede. A escolha é muitas vezes a forma como deseja gerir os seus recursos Azure.

* Se quiser gerir O AZure AD DS e VMs conectados como um grupo de recursos, pode criar uma sub-rede de rede virtual adicional para VMs.
* Se quiser separar a gestão do Azure AD DS e, em seguida, quaisquer VMs conectados, pode utilizar o espreitamento de rede virtual.
    * Também pode optar por utilizar o espreitamento de rede virtual para fornecer conectividade aos VM existentes no seu ambiente Azure que estão ligados a uma rede virtual existente.

Neste tutorial, basta configurar uma destas opções de conectividade de rede virtual.

Para obter mais informações sobre como planear e configurar a rede virtual, consulte [considerações de networking para os Serviços de Domínio do Diretório Ativo Azure][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Criar uma sub-rede de rede virtual

Por predefinição, a rede virtual Azure criada com o domínio gerido contém uma única sub-rede de rede virtual. Esta sub-rede de rede virtual só deve ser utilizada pela plataforma Azure para fornecer serviços de domínio geridos. Para criar e utilizar os seus próprios VMs nesta rede virtual Azure, crie uma sub-rede adicional.

Para criar uma sub-rede de rede virtual para VMs e cargas de trabalho de aplicação, complete os seguintes passos:

1. No portal Azure, selecione o grupo de recursos do seu domínio gerido, como *o myResourceGroup*. A partir da lista de recursos, escolha a rede virtual padrão, como *aadds-vnet*.
1. No menu à esquerda da janela de rede virtual, selecione **Espaço de endereço**. A rede virtual é criada com um espaço de endereço único de *10.0.2.0/24*, que é utilizada pela sub-rede predefinido.

    Adicione um intervalo adicional de endereços IP à rede virtual. A dimensão desta gama de endereços e a gama de endereços IP real para utilizar dependem de outros recursos de rede já implantados. O intervalo de endereços IP não deve sobrepor-se a quaisquer intervalos de endereços existentes no seu ambiente Azure ou no local. Certifique-se de que dimensiona o intervalo de endereço IP suficientemente grande para o número de VMs que espera implantar na sub-rede.

    No exemplo seguinte, é adicionado um intervalo adicional de endereço IP de *10.0.3.0/24.* Quando estiver pronto, **selecione Guardar**.

    ![Adicione uma gama adicional de endereços IP de rede virtual no portal Azure](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Em seguida, no menu à esquerda da janela de rede virtual, selecione **Subnetas** e, em seguida, escolha **+ Subnet** para adicionar uma sub-rede.
1. Introduza um nome para a sub-rede, como *cargas de trabalho.* Se necessário, atualize o **intervalo de Endereços** se pretender utilizar um subconjunto do intervalo de endereços IP configurado para a rede virtual nos passos anteriores. Por enquanto, deixe os predefinidos para opções como grupo de segurança de rede, tabela de rotas, pontos finais de serviço.

    No exemplo seguinte, é criada uma sub-rede chamada *cargas de trabalho* que utiliza a gama de endereços *IP 10.0.0/24:*

    ![Adicione uma sub-rede virtual adicional no portal Azure](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Quando estiver pronto, selecione **OK**. Leva alguns momentos para criar a sub-rede de rede virtual.

Quando criar um VM que precisa de utilizar o domínio gerido, certifique-se de que seleciona esta sub-rede de rede virtual. Não crie VMs na *sub-rede de aadds predefinidos*. Se selecionar uma rede virtual diferente, não há conectividade de rede e resolução de DNS para chegar ao domínio gerido, a menos que configurá o olhar da rede virtual.

## <a name="configure-virtual-network-peering"></a>Configurar o espreitamento da rede virtual

Pode ter uma rede virtual Azure existente para VMs ou pretender manter a sua rede virtual de domínio gerido separada. Para utilizar o domínio gerido, os VMs noutras redes virtuais precisam de uma forma de comunicar com os controladores de domínio Azure AD DS. Esta conectividade pode ser fornecida usando o olhar da rede virtual Azure.

Com o azure virtual network peering, duas redes virtuais estão conectadas entre si, sem a necessidade de um dispositivo de rede privada virtual (VPN). O espreitamento em rede permite-lhe ligar rapidamente redes virtuais e definir fluxos de tráfego através do seu ambiente Azure.

Para obter mais informações sobre o seu espreitamento, consulte [a visão geral da rede virtual Azure][peering-overview].

Para espreitar uma rede virtual para a rede virtual de domínio gerido, complete os seguintes passos:

1. Escolha a rede virtual padrão criada para o seu domínio gerido denominado *aadds-vnet*.
1. No menu esquerdo da janela de rede virtual, **selecione Peerings**.
1. Para criar um espreitante, selecione **+ Adicionar**. No exemplo seguinte, o *aadds-vnet* padrão é espreitado para uma rede virtual chamada *myVnet*. Configure as seguintes definições com os seus próprios valores:

    * **Nome do espreitamento de aadds-vnet para rede virtual remota**: Um identificador descritivo das duas redes, tais como *aadds-vnet-to-myvnet*
    * **Tipo de implementação de rede virtual**: *Gestor de Recursos*
    * **Subscrição**: A subscrição da rede virtual a que pretende espreitar, como *o Azure*
    * **Rede virtual**: A rede virtual a que pretende espreitar, como *o myVnet*
    * **Nome do que espreita do myVnet para aadds-vnet**: Um identificador descritivo das duas redes, tais como *myvnet-to-aadds-vnet*

    ![Configurar a rede virtual espreitando no portal Azure](./media/tutorial-configure-networking/create-peering.png)

    Deixe quaisquer outros predefinidos para acesso à rede virtual ou tráfego reencaminhado, a menos que tenha requisitos específicos para o seu ambiente, então selecione **OK**.

1. Leva alguns momentos para criar o espreitamento tanto na rede virtual AZure AD DS como na rede virtual que selecionou. Quando pronto, os **relatórios de estado de peering** *ligados,* como mostrado no seguinte exemplo:

    ![Redes com sucesso ligadas ao portal Azure](./media/tutorial-configure-networking/connected-peering.png)

Antes de os VMs na rede virtual esprevada poderem utilizar o domínio gerido, configurar os servidores DNS para permitir uma resolução correta do nome.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Configurar servidores DNS na rede virtual espreitada

Para vMs e aplicações na rede virtual esprevada para falar com sucesso com o domínio gerido, as definições de DNS devem ser atualizadas. Os endereços IP dos controladores de domínio Azure AD DS devem ser configurados como os servidores DNS na rede virtual. Existem duas formas de configurar os controladores de domínio como servidores DNS para a rede virtual espreitada:

* Configure os servidores DNS de rede virtual Azure para utilizar os controladores de domínio Azure AD DS.
* Configure o servidor DNS existente em uso na rede virtual espreigada para utilizar o encaminhamento de DNS condicional para consultas diretas para o domínio gerido. Estes passos variam dependendo do servidor DNS existente em uso.

Neste tutorial, vamos configurar os servidores DNS da rede virtual Azure para direcionar todas as consultas para os controladores de domínio Azure AD DS.

1. No portal Azure, selecione o grupo de recursos da rede virtual, como o *myResourceGroup*. A partir da lista de recursos, escolha a rede virtual espreitada, como *o myVnet.*
1. No menu esquerdo da janela de rede virtual, selecione **servidores DNS**.
1. Por predefinição, uma rede virtual utiliza os servidores DNS fornecidos pelo Azure incorporados. Opte por utilizar servidores DNS **personalizados.** Introduza os endereços IP para os controladores de domínio Azure AD DS, que normalmente são *10.0.2.4* e *10.0.2.5*. Confirme estes endereços IP na janela **'Vista Geral'** do seu domínio gerido no portal.

    ![Configure os servidores DNS de rede virtual para utilizar os controladores de domínio Azure AD DS](./media/tutorial-configure-networking/custom-dns.png)

1. Quando estiver pronto, **selecione Guardar**. Leva alguns momentos para atualizar os servidores DNS para a rede virtual.
1. Para aplicar as definições de DNS atualizadas nos VMs, reinicie os VMs ligados à rede virtual.

Quando criar um VM que precisa de utilizar o domínio gerido, certifique-se de que seleciona esta rede virtual. Se selecionar uma rede virtual diferente, não há conectividade de rede e resolução de DNS para chegar ao domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Compreenda as opções de conectividade de rede virtual para recursos unidos pelo domínio ao Azure AD DS
> * Criar uma gama de endereços IP e sub-redes adicionais na rede virtual Azure AD DS
> * Configure a rede virtual que espreita para uma rede separada do Azure AD DS

Para ver este domínio gerido em ação, crie e junte uma máquina virtual ao domínio.

> [!div class="nextstepaction"]
> [Junte uma máquina virtual do Windows Server ao seu domínio gerido](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
