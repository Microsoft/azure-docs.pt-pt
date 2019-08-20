---
title: Tutorial-criar uma VM de gerenciamento para Azure Active Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar e configurar uma máquina virtual do Windows que você usa para administrar Azure Active Directory Domain Services instância do.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: e7c3ccb553010b84a30ccdad875ea0362112d830
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619097"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Criar uma VM de gerenciamento para configurar e administrar um Azure Active Directory Domain Services domínio gerenciado

O Azure Active Directory Domain Services (AD DS) fornece serviços de domínio gerenciado, como ingresso no domínio, diretiva de grupo, LDAP e autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server Active Directory. Você administra esse domínio gerenciado usando o mesmo Ferramentas de Administração de Servidor Remoto (RSAT) como com um domínio de Active Directory Domain Services local. Como o Azure AD DS é um serviço gerenciado, há algumas tarefas administrativas que você não pode executar, como usar o protocolo RDP para se conectar aos controladores de domínio.

Este tutorial mostra como criar uma VM do Windows Server no Azure e instalar as ferramentas necessárias para administrar um domínio gerenciado do Azure AD DS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Entender as tarefas administrativas disponíveis em um domínio gerenciado AD DS do Azure
> * Instalar as ferramentas administrativas do Active Directory em uma VM do Windows Server
> * Usar o Centro Administrativo do Active Directory para executar tarefas comuns

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma VM do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
    * Se necessário, o tutorial anterior [cria uma VM do Windows Server e a une a um domínio gerenciado][create-join-windows-vm].
* Uma conta de usuário que é membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure AD.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, você criará e configurará uma VM de gerenciamento usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Tarefas administrativas disponíveis no Azure AD DS

O Azure AD DS fornece um domínio gerenciado para os usuários, aplicativos e serviços a serem consumidos. Essa abordagem altera algumas das tarefas de gerenciamento disponíveis que você pode fazer e quais privilégios você tem dentro do domínio gerenciado. Essas tarefas e permissões podem ser diferentes do que você enfrenta com um ambiente de Active Directory Domain Services local regular. Você também não pode se conectar aos controladores de domínio no domínio gerenciado AD DS do Azure usando Área de Trabalho Remota.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Tarefas administrativas que você pode executar em um domínio gerenciado AD DS do Azure

Os membros do grupo de *Administradores do AAD DC* recebem privilégios no domínio gerenciado AD DS do Azure que permite que eles realizem tarefas como:

* Ingresse computadores no domínio gerenciado.
* Configure o GPO (objeto de diretiva de grupo) interno para os contêineres *computadores AADDC* e *usuários AADDC* no domínio gerenciado.
* Administrar o DNS no domínio gerido.
* Crie e administre UOs (unidades organizacionais) personalizadas no domínio gerenciado.
* Obter acesso administrativo aos computadores associados ao domínio gerido.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Privilégios administrativos que você não tem em um domínio gerenciado do Azure AD DS

O domínio gerenciado AD DS do Azure é bloqueado, portanto, você não tem privilégios para realizar determinadas tarefas administrativas no domínio. Alguns dos exemplos a seguir são tarefas que você não pode fazer:

* Estenda o esquema do domínio gerenciado.
* Conecte-se a controladores de domínio para o domínio gerenciado usando Área de Trabalho Remota.
* Adicione controladores de domínio ao domínio gerenciado.
* Você não tem privilégios de administrador de *domínio* ou de *administrador corporativo* para o domínio gerenciado.

## <a name="sign-in-to-the-windows-server-vm"></a>Entrar na VM do Windows Server

No tutorial anterior, uma VM do Windows Server foi criada e unida ao domínio gerenciado AD DS do Azure. Vamos usar essa VM para instalar as ferramentas de gerenciamento. Se necessário, [siga as etapas no tutorial para criar e ingressar uma VM do Windows Server em um domínio gerenciado][create-join-windows-vm].

> [!NOTE]
> Neste tutorial, você usa uma VM do Windows Server no Azure que é unida ao domínio gerenciado AD DS do Azure. Você também pode usar um cliente do Windows, como o Windows 10, que é ingressado no domínio gerenciado.
>
> Para obter mais informações sobre como instalar as ferramentas administrativas em um cliente Windows, consulte [instalar o ferramentas de administração de servidor remoto (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Para começar, conecte-se à VM do Windows Server da seguinte maneira:

1. No portal do Azure, selecione **grupos de recursos** no lado esquerdo. Escolha o grupo de recursos em que sua VM foi criada,como MyResource Group e, em seguida, selecione a VM, como *myVM*.
1. Nas janelas de **visão geral** da VM, selecione **conectar**.

    ![Conectar-se à máquina virtual do Windows no portal do Azure](./media/tutorial-create-management-vm/connect-vm.png)

    Você também pode [criar e usar um host de bastiões do Azure (atualmente em versão prévia)][azure-bastion] para permitir o acesso somente por meio do portal do Azure sobre SSL.

1. Selecione a opção para *baixar o arquivo RDP*. Salve este arquivo RDP em seu navegador da Web.
1. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for pedido, selecione **Ligar**.
1. Insira as credenciais de um usuário que faz parte do grupo de *Administradores de DC do Azure ad* , como *contoso\dee*
1. Se você vir um aviso de certificado durante o processo de entrada, selecione **Sim** ou **continuar** a se conectar.

## <a name="install-active-directory-administrative-tools"></a>Instalar Active Directory ferramentas administrativas

Os domínios gerenciados AD DS do Azure são gerenciados usando as mesmas ferramentas administrativas que os ambientes de AD DS locais, como o Centro Administrativo do Active Directory (ADAC) ou o AD PowerShell. Essas ferramentas podem ser instaladas como parte do recurso Ferramentas de Administração de Servidor Remoto (RSAT) em computadores cliente e Windows Server. Os membros do grupo de *Administradores do AAD DC* podem administrar remotamente os domínios gerenciados do Azure AD DS usando essas ferramentas administrativas do AD de um computador que tenha ingressado no domínio gerenciado.

Para instalar as ferramentas de administração de Active Directory em uma VM ingressada no domínio, conclua as seguintes etapas:

1. **Gerenciador do servidor** deve abrir por padrão quando você entra na VM. Se não estiver, no menu **Iniciar** , selecione **Gerenciador do servidor**.
1. No painel painel da janela **Gerenciador do servidor** , selecione **adicionar funções e recursos**.
1. Na página **antes de começar** do *Assistente para adicionar funções e recursos*, selecione **Avançar**.
1. Para o *tipo de instalação*, deixe a opção de instalação baseada em **função ou recurso** marcada e selecione **Avançar**.
1. Na página **seleção de servidor** , escolha a VM atual no pool de servidores, como *MyVM.contoso.com*, e selecione **Avançar**.
1. Na página **funções de servidor** , clique em **Avançar**.
1. Na página **recursos** , expanda o nó **ferramentas de administração de servidor remoto** e, em seguida, expanda o nó **ferramentas de administração de função** .

    Escolha **AD DS e AD LDS recurso ferramentas** na lista de ferramentas de administração de função e, em seguida, selecione **Avançar**.

    ![Instalar o ' AD DS e as ferramentas de AD LDS ' na página de recursos](./media/tutorial-create-management-vm/install-features.png)

1. Na página **confirmação** , selecione **instalar**. Pode levar um minuto ou dois para instalar as ferramentas administrativas.
1. Quando a instalação do recurso estiver concluída, selecione **fechar** para sair do assistente **adicionar funções e recursos** .

## <a name="use-active-directory-administrative-tools"></a>Usar Active Directory ferramentas administrativas

Com as ferramentas administrativas instaladas, vejamos como usá-las para administrar o domínio gerenciado AD DS do Azure. Verifique se você está conectado à VM com uma conta de usuário que seja membro do grupo de administradores de *DC do AAD* .

1. No menu **Iniciar** , selecione **Ferramentas administrativas do Windows**. As ferramentas administrativas do AD instaladas na etapa anterior são listadas.

    ![Lista de ferramentas administrativas instaladas no servidor](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Selecione **centro administrativo do Active Directory**.
1. Para explorar o domínio gerenciado AD DS do Azure, escolha o nome de domínio no painel esquerdo, como *contoso.com*. Dois contêineres denominados *AADDC Computers* e *AADDC Users* estão na parte superior da lista.

    ![Listar a parte de contêineres disponíveis do domínio gerenciado AD DS do Azure](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Para ver os usuários e grupos que pertencem ao domínio gerenciado AD DS do Azure, selecione o contêiner **AADDC Users** . As contas de usuário e grupos do seu locatário do Azure AD são listados neste contêiner.

    Na saída de exemplo a seguir, uma conta de usuário chamada *contosoadmin* e um grupo para *Administradores do AAD DC* são mostrados neste contêiner.

    ![Exibir a lista de usuários de domínio do Azure AD DS no Centro Administrativo do Active Directory](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Para ver os computadores que ingressaram no domínio gerenciado AD DS do Azure, selecione o contêiner **computadores AADDC** . Uma entrada para a máquina virtual atual, como *myVM*, é listada. As contas de computador para todos os computadores que ingressaram no domínio gerenciado AD DS do Azure são armazenadas nesse contêiner de *computadores AADDC* .

Ações comuns de Centro Administrativo do Active Directory, como redefinição de uma senha de conta de usuário ou gerenciamento de associação de grupo, estão disponíveis. Você também pode usar o *módulo Active Directory para o Windows PowerShell*, instalado como parte das ferramentas administrativas, para gerenciar ações comuns em seu domínio gerenciado do AD DS do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Entender as tarefas administrativas disponíveis em um domínio gerenciado AD DS do Azure
> * Instalar as ferramentas administrativas do Active Directory em uma VM do Windows Server
> * Usar o Centro Administrativo do Active Directory para executar tarefas comuns

Para interagir com segurança com seu domínio gerenciado do AD DS do Azure, habilite o protocolo LDAP (Lightweight Directory Access Protocol) seguro.

> [!div class="nextstepaction"]
> [Configurar o LDAP seguro para seu domínio gerenciado](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
