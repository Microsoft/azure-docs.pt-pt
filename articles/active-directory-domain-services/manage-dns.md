---
title: Gerir o DNS para serviços de domínio azure ad [ Microsoft Docs
description: Aprenda a instalar as Ferramentas de Servidor DNS para gerir o DNS para um domínio gerido pelo Azure Ative Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 2694a5f250b746748a1b42ac4d211aa28ef1ebad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613686"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Administrar dNS num domínio gerido pelos Serviços de Domínio Azure AD

Nos Serviços de Domínio de Diretório Ativo Azure (Azure AD DS), um componente chave é o DNS (Resolução de Nome de Domínio). O Azure AD DS inclui um servidor DNS que fornece resolução de nome para o domínio gerido. Este servidor DNS inclui registos dNS incorporados e atualizações para os componentes chave que permitem que o serviço seja executado.

À medida que executa as suas próprias aplicações e serviços, poderá ter de criar registos DNS para máquinas que não estejam unidas ao domínio, configurar endereços IP virtuais para equilibradores de carga ou configurar avançados externos do DNS. Os utilizadores que pertencem ao grupo de administradores da *AAD DC* recebem privilégios de administração DNS no domínio gerido pelo Azure AD DS e podem criar e editar registos dNS personalizados.

Num ambiente híbrido, as zonas de DNS e os registos configurados num ambiente AD DS no local não são sincronizados com O DS Azure. Para definir e utilizar as suas próprias entradas dNS, crie registos no servidor DNS DNS Da AD Azure ou utilize avançados condicionados que apontem para servidores DNS existentes no seu ambiente.

Este artigo mostra-lhe como instalar as ferramentas DNS Server e depois utilizar a consola DNS para gerir registos em DS AD Azure.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, complete o tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
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

    ![Escolha instalar as Ferramentas de Servidor DNS a partir da lista de ferramentas de administração de funções disponíveis](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Na página **confirmação,** selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas de Gestão de Políticas do Grupo.
1. Quando a instalação da funcionalidade estiver concluída, selecione **Close** to exit the **Add Roles and Features** wizard.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Abra a consola de gestão DNS para administrar dNS

Com as ferramentas DNS Server instaladas, pode administrar registos DNS no domínio gerido pelo Azure AD DS.

> [!NOTE]
> Para administrar dNS num domínio gerido pelo Azure AD DS, deve ser inscrito numa conta de utilizador que seja membro do grupo de administradores da *AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É apresentada uma lista de ferramentas de gestão disponíveis, incluindo **DNS** instalados na secção anterior. Selecione **DNS** para lançar a consola DNS Management.
1. No diálogo **Connect to DNS Server,** selecione **O seguinte computador,** introduza o nome de domínio DNS do domínio gerido, como *aaddscontoso.com:*

    ![Ligue-se ao domínio gerido pelo Azure AD DS na consola DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. A consola DNS liga-se ao domínio gerido pelo Azure AD DS especificado. Expanda as Zonas de **Alookup Para A Frente** ou Zonas de **Procura Reversa** para criar as entradas dNS necessárias ou editar os registos existentes conforme necessário.

    ![Consola DNS - domínio de administração](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Quando gere os registos utilizando as ferramentas do DNS Server, certifique-se de que não elimina ou modifica os registos DNS incorporados que são utilizados pelo Azure AD DS. Os registos dNS incorporados incluem registos de DNS de domínio, registos de servidores de nomee outros registos utilizados para a localização de DC. Se modificar estes registos, os serviços de domínio são interrompidos na rede virtual.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão do DNS, consulte o artigo de [ferramentas DNS no Technet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
