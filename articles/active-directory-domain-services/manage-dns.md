---
title: Gerir DNS para Azure AD Domain Services | Microsoft Docs
description: Aprenda a instalar as Ferramentas do Servidor DNS para gerir o DNS e criar reencaminhadores condicional para um domínio gerido por Serviços de Domínio do Diretório Ativo Azure.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: afa6920a36a5a7218571239b36815004d8f2d450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619356"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrar DNS e criar reencaminhadores condicionalistas num domínio gerido por Serviços de Domínio do Diretório Ativo Azure

Nos Serviços de Domínio do Diretório Ativo Azure (Azure AD DS), um componente chave é DNS (Resolução de Nome de Domínio). O Azure AD DS inclui um servidor DNS que fornece resolução de nome para o domínio gerido. Este servidor DNS inclui registos DNS incorporados e atualizações para os componentes-chave que permitem executar o serviço.

À medida que executam as suas próprias aplicações e serviços, poderá ter de criar registos DNS para máquinas que não estejam unidas ao domínio, configurar endereços IP virtuais para esquilibradores de carga ou configurar reencaminhadores de DNS externos. Os utilizadores que pertencem ao grupo *de administradores da AAD DC* recebem privilégios de administração dns no domínio gerido Azure AD DS e podem criar e editar registos DNS personalizados.

Num ambiente híbrido, as zonas de DNS e registos configurados em outros espaços de nomes DNS, como um ambiente AD DS no local, não são sincronizados com o domínio gerido. Para resolver recursos nomeados em outros espaços de nome DNS, crie e use reencaminhadores condicional que apontam para os servidores DNS existentes no seu ambiente.

Este artigo mostra-lhe como instalar as ferramentas DNS Server e, em seguida, utilizar a consola DNS para gerir registos e criar reencaminhadores condicionalistas em Azure AD DS.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Conectividade da sua rede virtual Azure AD DS até onde estão hospedados os seus outros espaços de nome DNS.
    * Esta conectividade pode ser fornecida com uma ligação [Azure ExpressRoute][expressroute] ou [Azure VPN Gateway.][vpn-gateway]
* Um VM de gestão de servidores do Windows que se junta ao domínio gerido.
    * Se necessário, complete o tutorial para [criar um VM do Servidor do Windows e junte-o a um domínio gerido][create-join-windows-vm].
* Uma conta de utilizador que é membro do grupo de administradores da *Ad DC Azure* no seu inquilino AZure AD.

## <a name="install-dns-server-tools"></a>Instalar ferramentas dns server

Para criar e modificar registos DNS num domínio gerido, é necessário instalar as ferramentas dns Server. Estas ferramentas podem ser instaladas como uma funcionalidade no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas num cliente Windows, consulte instalar [Ferramentas de Administração de Servidor Remoto (RSAT)][install-rsat].

1. Inscreva-se na sua gestão VM. Para obter etapas sobre como ligar utilizando o portal Azure, consulte [Connect to a Windows Server VM][connect-windows-server-vm].
1. Se **o Gestor do Servidor** não abrir por predefinição quando iniciar serção no VM, selecione o menu **Iniciar** e, em seguida, escolha o Gestor **do Servidor**.
1. No *painel de instrumentos* da janela **'Gestor do servidor',** selecione **Adicionar Funções e Funcionalidades**.
1. Na **página antes** de começar a página do Assistente de *Funções e Funcionalidades adicionais*, selecione **Seguinte**.
1. Para o *Tipo de Instalação*, deixe a opção **de instalação baseada em funções ou baseada em recursos** verificada e selecione **Seguinte**.
1. Na página **'Seleção** do Servidor', escolha o VM atual a partir da piscina do servidor, como *myvm.aaddscontoso.com,* selecione **Next**.
1. Na página **'Funções do Servidor',** clique em **'Seguinte'.**
1. Na página **Funcionalidades,** expanda o nó **de Ferramentas de Administração do Servidor Remoto** e, em seguida, expanda o nó **ferramentas de administração** de funções. Selecione a funcionalidade **FERRAMENTAS de Servidor DNS** da lista de ferramentas de administração de funções.

    ![Opte por instalar as Ferramentas do Servidor DNS a partir da lista de ferramentas de administração de funções disponíveis](./media/manage-dns/install-dns-tools.png)

1. Na página **De Confirmação,** selecione **Instalar**. Pode levar um minuto ou dois para instalar as Ferramentas do Servidor DNS.
1. Quando a instalação da funcionalidade estiver concluída, selecione **Close** to exit the **Add Roles and Features** wizard.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Abra a consola de gestão DNS para administrar DNS

Com as ferramentas dns Server instaladas, pode administrar registos DNS no domínio gerido.

> [!NOTE]
> Para administrar o DNS num domínio gerido, tem de ser inscrito numa conta de utilizador que seja membro do grupo *de Administradores AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É apresentada uma lista de ferramentas de gestão disponíveis, incluindo **DNS** instaladas na secção anterior. Selecione **DNS** para lançar a consola DNS Management.
1. No diálogo **'Connect to DNS Server',** selecione **O seguinte computador**, em seguida, introduza o nome de domínio DNS do domínio gerido, como *aaddscontoso.com*:

    ![Conecte-se ao domínio gerido na consola DNS](./media/manage-dns/connect-dns-server.png)

1. A Consola DNS liga-se ao domínio gerido especificado. Expanda as **Zonas de Procura para a Frente** ou Zonas de Procura inversa para criar as suas entradas de DNS **necessárias** ou editar os registos existentes conforme necessário.

    ![DNS Console - administrar domínio](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Quando gerir registos utilizando as ferramentas DNS Server, certifique-se de que não elimina ou modifica os registos DNS incorporados que são utilizados pelo Azure AD DS. Os registos DNS incorporados incluem registos DNS de domínio, registos de servidores de nomes e outros registos utilizados para a localização de DC. Se modificar estes registos, os serviços de domínio são interrompidos na rede virtual.

## <a name="create-conditional-forwarders"></a>Criar reencaminhadores condicionais

Uma zona Azure AD DS DNS deve conter apenas a zona e os registos do domínio gerido em si. Não crie zonas adicionais no domínio gerido para resolver recursos nomeados em outros espaços de nome DNS. Em vez disso, utilize reencaminhadores condicionalistas no domínio gerido para dizer ao servidor DNS para onde ir para resolver endereços para esses recursos.

Um reencaminhador condicional é uma opção de configuração num servidor DNS que permite definir um domínio DNS, como *contoso.com*, para encaminhar consultas para. Em vez do servidor DNS local tentar resolver consultas de registos nesse domínio, as consultas de DNS são reencaminhadas para o DNS configurado para esse domínio. Esta configuração garante que os registos DNS corretos são devolvidos, uma vez que não cria uma zona de DNS local com registos duplicados no domínio gerido para refletir esses recursos.

Para criar um reencaminhador condicional no seu domínio gerido, complete os seguintes passos:

1. Selecione a sua zona DNS, como *aaddscontoso.com*.
1. Selecione **Os Avançados Condicionais,** em seguida, selecione à direita e escolha **novo avançado condicionado...**
1. Introduza o seu outro **Domínio DNS**, como *contoso.com*, insira os endereços IP dos servidores DNS para esse espaço de nome, como mostra o seguinte exemplo:

    ![Adicione e configuure um reencaminhador condicional para o servidor DNS](./media/manage-dns/create-conditional-forwarder.png)

1. Verifique a caixa para **guardar este reencaminhador condicional no Ative Directory e replique-o da seguinte forma**, selecione a opção para todos os *servidores DNS neste domínio*, como mostra o seguinte exemplo:

    ![DNS Console - selecione todos os servidores DNS neste domínio](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Se o reencaminhador condicional for armazenado na *floresta* em vez do *domínio,* o reencaminhador condicional falha.

1. Para criar o reencaminhador condicional, selecione **OK**.

A resolução de nomes dos recursos em outros espaços de nome de VMs ligados ao domínio gerido deve agora resolver-se corretamente. As consultas para o domínio DNS configuradas no reencaminhador condicional são transmitidas aos servidores DNS relevantes.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão do DNS, consulte o [artigo de ferramentas DNS na Technet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11)).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh