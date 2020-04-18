---
title: Gerir o DNS para serviços de domínio azure ad [ Microsoft Docs
description: Aprenda a instalar as Ferramentas de Servidor DNS para gerir o DNS e criar encaminhadores condicionais para um domínio gerido pelo Azure Ative Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2020
ms.author: iainfou
ms.openlocfilehash: f4bd3f75c3246cb11e88dbaae817eba8ac76b394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603504"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-ad-domain-services-managed-domain"></a>Administrar o DNS e criar avançados condicional num domínio gerido pelos Serviços de Domínio Azure AD

Nos Serviços de Domínio de Diretório Ativo Azure (Azure AD DS), um componente chave é o DNS (Resolução de Nome de Domínio). O Azure AD DS inclui um servidor DNS que fornece resolução de nome para o domínio gerido. Este servidor DNS inclui registos dNS incorporados e atualizações para os componentes chave que permitem que o serviço seja executado.

À medida que executa as suas próprias aplicações e serviços, poderá ter de criar registos DNS para máquinas que não estejam unidas ao domínio, configurar endereços IP virtuais para equilibradores de carga ou configurar avançados externos do DNS. Os utilizadores que pertencem ao grupo de administradores da *AAD DC* recebem privilégios de administração DNS no domínio gerido pelo Azure AD DS e podem criar e editar registos dNS personalizados.

Num ambiente híbrido, as zonas dNS e registos configurados em outros espaços de nome DNS, como um ambiente AD DS no local, não são sincronizados com DS AD Azure. Para resolver os recursos nomeados em outros espaços de nome DNS, crie e use avançados condicionados que apontem para servidores DNS existentes no seu ambiente.

Este artigo mostra-lhe como instalar as ferramentas DNS Server e depois usar a consola DNS para gerir registos e criar avançados condicionados em DS ADS Azure.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, complete o tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Conectividade da sua rede virtual Azure AD DS até onde estão hospedados os seus outros espaços de nome DNS.
    * Esta conectividade pode ser fornecida com uma ligação [Azure ExpressRoute][expressroute] ou [Azure VPN Gateway.][vpn-gateway]
* Um VM de gestão do Servidor Windows que se junta ao domínio gerido pelo Azure AD DS.
    * Se necessário, complete o tutorial para [criar um VM do Servidor windows e junte-o a um domínio gerido][create-join-windows-vm].
* Uma conta de utilizador que é membro do grupo de administradores da *Azure AD DC* no seu inquilino Azure AD.

## <a name="install-dns-server-tools"></a>Instalar ferramentas do Servidor DNS

Para criar e modificar os registos DNS em DS AD S Azure, é necessário instalar as ferramentas do Servidor DNS. Estas ferramentas podem ser instaladas como uma funcionalidade no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas num cliente windows, consulte a instalação de Ferramentas de Administração do [Servidor Remoto (RSAT)][install-rsat].

1. Inscreva-se na sua VM de gestão. Para obter passos sobre como se conectar utilizando o portal Azure, consulte [Connect to a Windows Server VM][connect-windows-server-vm].
1. Se o **Gestor do Servidor** não abrir por padrão quando iniciar sessão no VM, selecione o menu **Iniciar** e, em seguida, escolha o Gestor **do Servidor**.
1. No painel do *Dashboard* da janela Do Gestor do **Servidor,** selecione **Adicionar Funções e Funcionalidades**.
1. Na página Antes de **Iniciar** o Assistente de *Adicionar Funções e Funcionalidades,* selecione **Next**.
1. Para o Tipo de *Instalação,* deixe a opção **de instalação baseada em funções** ou baseada em funções verificada e selecione **Next**.
1. Na página de Seleção do **Servidor,** escolha o VM atual a partir do conjunto de servidores, como *myvm.aaddscontoso.com,* em seguida, selecione **Next**.
1. Na página Funções do **Servidor,** clique em **Seguinte**.
1. Na página **Funcionalidades,** expanda o nó de **Ferramentas** de Administração do Servidor Remoto e, em seguida, expanda o nó de Ferramentas de Administração de **Funções.** Selecione a funcionalidade **DNS Server Tools** da lista de ferramentas de administração de funções.

    ![Escolha instalar as Ferramentas de Servidor DNS a partir da lista de ferramentas de administração de funções disponíveis](./media/manage-dns/install-dns-tools.png)

1. Na página **confirmação,** selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas de Gestão de Políticas do Grupo.
1. Quando a instalação da funcionalidade estiver concluída, selecione **Close** to exit the **Add Roles and Features** wizard.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Abra a consola de gestão DNS para administrar dNS

Com as ferramentas DNS Server instaladas, pode administrar registos DNS no domínio gerido pelo Azure AD DS.

> [!NOTE]
> Para administrar dNS num domínio gerido pelo Azure AD DS, deve ser inscrito numa conta de utilizador que seja membro do grupo de administradores da *AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É apresentada uma lista de ferramentas de gestão disponíveis, incluindo **DNS** instalados na secção anterior. Selecione **DNS** para lançar a consola DNS Management.
1. No diálogo **Connect to DNS Server,** selecione **O seguinte computador,** introduza o nome de domínio DNS do domínio gerido, como *aaddscontoso.com:*

    ![Ligue-se ao domínio gerido pelo Azure AD DS na consola DNS](./media/manage-dns/connect-dns-server.png)

1. A consola DNS liga-se ao domínio gerido pelo Azure AD DS especificado. Expanda as Zonas de **Alookup Para A Frente** ou Zonas de **Procura Reversa** para criar as entradas dNS necessárias ou editar os registos existentes conforme necessário.

    ![Consola DNS - domínio de administração](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Quando gere os registos utilizando as ferramentas do DNS Server, certifique-se de que não elimina ou modifica os registos DNS incorporados que são utilizados pelo Azure AD DS. Os registos dNS incorporados incluem registos de DNS de domínio, registos de servidores de nomee outros registos utilizados para a localização de DC. Se modificar estes registos, os serviços de domínio são interrompidos na rede virtual.

## <a name="create-conditional-forwarders"></a>Criar avançados condicional

Uma zona DNS DNs AD Azure deve conter apenas a zona e os registos para o domínio gerido em si. Não crie zonas adicionais em Azure AD DS para resolver os recursos nomeados em outros espaços de nome DNS. Em vez disso, utilize os avançados condicionados no domínio gerido pelo Azure AD DS para dizer ao servidor DNS para onde ir para resolver endereços para esses recursos.

Um avançado condicional é uma opção de configuração num servidor DNS que permite definir um domínio DNS, como *contoso.com,* para encaminhar consultas para. Em vez do servidor DNS local a tentar resolver consultas de registos nesse domínio, as consultas de DNS são reencaminhadas para o DNS configurado para esse domínio. Esta configuração garante que os registos DNS corretos são devolvidos, uma vez que não cria uma zona DNS local com registos duplicados no domínio gerido pelo Azure AD DS para refletir esses recursos.

Para criar um avançado condicional no seu domínio gerido pelo Azure AD DS, complete os seguintes passos:

1. Selecione a sua zona DNS DNs Azure AD DNs, como *aaddscontoso.com*.vb
1. Selecione **Forwarders Condicionados,** em seguida, selecione à direita e escolha **Novo Avançado Condicional...**
1. Introduza o seu outro **domínio DNS**, como *contoso.com*, e introduza os endereços IP dos servidores DNS para esse espaço de nome, como mostra o seguinte exemplo:

    ![Adicione e configure um avançado condicional para o servidor DNS](./media/manage-dns/create-conditional-forwarder.png)

1. Verifique a caixa para **Armazenar este encaminhador condicional no Diretório Ativo e reproduza-o da seguinte forma,** em seguida, selecione a opção para *todos os servidores DNS neste domínio,* como mostra o seguinte exemplo:

    ![Consola DNS - domínio de administração](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Se o avançado condicional for armazenado na *floresta* em vez do *domínio,* o avançado condicional falha.

1. Para criar o avançado condicional, selecione **OK**.

A resolução de nomes dos recursos em outros espaços de nome saem ligados ao domínio gerido pelo Azure AD DS deve agora ser resolvida corretamente. As consultas para o domínio DNS configuradas no reencaminhador condicional são passadas para os servidores DNS relevantes.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão do DNS, consulte o artigo de [ferramentas DNS no Technet](https://technet.microsoft.com/library/cc753579.aspx).

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
