---
title: Tutorial - Criar um VM de gestão para serviços de domínio de diretório ativo Azure [ Microsoft Docs
description: Neste tutorial, aprende-se a criar e configurar uma máquina virtual do Windows que utiliza para administrar a instância de Serviços de Domínio do Diretório Ativo azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 09fcf88c6dfe90380f387c6d72c751634f5b1606
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475790"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Criar um VM de gestão para configurar e administrar um domínio gerido pelo Azure Ative Directory Domain Services

Os Serviços de Domínio de Diretório Ativo Azure (AD DS) fornecem serviços de domínio geridos, tais como a adesão ao domínio, a política de grupo, a autenticação LDAP e Kerberos/NTLM que é totalmente compatível com o Diretório Ativo do Windows Server. Administra este domínio gerido utilizando as mesmas Ferramentas de Administração do Servidor Remoto (RSAT) como com um domínio de Serviços de Domínio de Diretório Ativo no local. Como o Azure AD DS é um serviço gerido, existem algumas tarefas administrativas que não pode executar, tais como a utilização de protocolo seletiva remota (RDP) para ligar aos controladores de domínio.

Este tutorial mostra-lhe como criar um VM do Windows Server em Azure e instalar as ferramentas necessárias para administrar um domínio gerido pelo Azure AD DS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Compreender as tarefas administrativas disponíveis num domínio gerido pela AD DS azure
> * Instale as ferramentas administrativas do Diretório Ativo num VM do Servidor windows
> * Utilize o Centro Administrativo de Diretório Ativo para executar tarefas comuns

Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, consulte o primeiro tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Um VM do Servidor windows que se junta ao domínio gerido pelo Azure AD DS.
    * Se necessário, consulte o tutorial anterior para [criar um VM do Windows Server e junte-o a um domínio gerido][create-join-windows-vm].
* Uma conta de utilizador que é membro do grupo de administradores da *Azure AD DC* no seu inquilino Azure AD.
* Um hospedeiro Azure Bastion implantado na sua rede virtual Azure AD DS.
    * Se necessário, [crie um anfitrião do Bastião Azure.][azure-bastion]

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria e configura um VM de gestão utilizando o portal Azure. Para começar, inicie a inscrição no [portal Azure.](https://portal.azure.com)

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Tarefas administrativas disponíveis em Azure AD DS

O Azure AD DS fornece um domínio gerido para os seus utilizadores, aplicações e serviços para consumir. Esta abordagem altera algumas das tarefas de gestão disponíveis que pode fazer e quais os privilégios que tem dentro do domínio gerido. Estas tarefas e permissões podem ser diferentes das que experimenta com um ambiente regular no local ative directory Domain Services. Também não é possível ligar-se a controladores de domínio no domínio gerido pelo Azure AD DS utilizando o Remote Desktop.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Tarefas administrativas que pode executar num domínio gerido por Azure AD DS

Os membros do grupo de administradores da *AAD DC* recebem privilégios no domínio gerido pela AD DS azure que lhes permite fazer tarefas como:

* Configure o objeto de política de grupo incorporado (GPO) para os recipientes *AADDC Computers* e *AADDC Users* no domínio gerido.
* Administrar o DNS no domínio gerido.
* Crie e disgere unidades organizacionais personalizadas (OUs) no domínio gerido.
* Obter acesso administrativo aos computadores associados ao domínio gerido.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Privilégios administrativos que não tem num domínio gerido pela AD DS azure

O domínio gerido pela AD DS azure está bloqueado, por isso não tem privilégios para fazer certas tarefas administrativas no domínio. Alguns dos seguintes exemplos são tarefas que não pode fazer:

* Estenda o esquema do domínio gerido.
* Ligue-se aos controladores de domínio para o domínio gerido utilizando o Remote Desktop.
* Adicione controladores de domínio ao domínio gerido.
* Não tem privilégios de Administrador de *Domínio* ou *Administrador de Empresa* para o domínio gerido.

## <a name="sign-in-to-the-windows-server-vm"></a>Iniciar sessão no VM do Servidor do Windows

No tutorial anterior, foi criado um VM do Windows Server e juntou-se ao domínio gerido pelo Azure AD DS. Vamos usar esse VM para instalar as ferramentas de gestão. Se necessário, [siga os passos no tutorial para criar e junte-se a um VM][create-join-windows-vm]do Windows Server para um domínio gerido .

> [!NOTE]
> Neste tutorial, utiliza um VM Do Servidor Windows em Azure que se junta ao domínio gerido pelo Azure AD DS. Também pode utilizar um cliente Windows, como o Windows 10, que se junta ao domínio gerido.
>
> Para obter mais informações sobre como instalar as ferramentas administrativas num cliente do Windows, consulte instalar ferramentas de administração do [servidor remoto (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Para começar, ligue-se ao VM do Servidor do Windows da seguinte forma:

1. No portal Azure, selecione **Grupos de Recursos** no lado esquerdo. Escolha o grupo de recursos onde o seu VM foi criado, como o *myResourceGroup,* e depois selecione o VM, como o *myVM*.
1. No painel **de visão geral** para o seu VM, selecione **Connect**, depois **Bastion**.

    ![Ligue-se à máquina virtual do Windows utilizando o Bastião no portal Azure](./media/join-windows-vm/connect-to-vm.png)

1. Introduza as credenciais para o seu VM e, em seguida, selecione **Connect**.

   ![Ligue-se através do anfitrião bastião no portal Azure](./media/join-windows-vm/connect-to-bastion.png)

Se necessário, permita que o seu navegador web abra pop-ups para que a ligação Bastião seja exibida. Leva alguns segundos para fazer a ligação com o seu VM.

## <a name="install-active-directory-administrative-tools"></a>Instalar ferramentas administrativas de Diretório Ativo

Os domínios geridos pelo Azure AD DS são geridos utilizando as mesmas ferramentas administrativas que os ambientes AD DS no local, como o Ative Directory Administrative Center (ADAC) ou a AD PowerShell. Estas ferramentas podem ser instaladas como parte da funcionalidade Remote Server Administration Tools (RSAT) no Windows Server e nos computadores clientes. Os membros do grupo de administradores da *AAD DC* podem então administrar domínios geridos pelo Azure AD DS remotamente utilizando estas ferramentas administrativas ad a partir de um computador que é unido ao domínio gerido.

Para instalar as ferramentas de Administração de Diretórios Ativos num VM filiado em domínio, complete os seguintes passos:

1. Se o **Gestor do Servidor** não abrir por padrão quando iniciar sessão no VM, selecione o menu **Iniciar** e, em seguida, escolha o Gestor **do Servidor**.
1. No painel do *Dashboard* da janela Do Gestor do **Servidor,** selecione **Adicionar Funções e Funcionalidades**.
1. Na página Antes de **Iniciar** o Assistente de *Adicionar Funções e Funcionalidades,* selecione **Next**.
1. Para o Tipo de *Instalação,* deixe a opção **de instalação baseada em funções** ou baseada em funções verificada e selecione **Next**.
1. Na página de Seleção do **Servidor,** escolha o VM atual a partir do conjunto de servidores, como *myvm.aaddscontoso.com,* em seguida, selecione **Next**.
1. Na página Funções do **Servidor,** clique em **Seguinte**.
1. Na página **Funcionalidades,** expanda o nó de **Ferramentas** de Administração do Servidor Remoto e, em seguida, expanda o nó de Ferramentas de Administração de **Funções.**

    Escolha a funcionalidade **AD DS e AD LDS Tools** da lista de ferramentas de administração de funções e, em seguida, selecione **Next**.

    ![Instale as 'Ferramentas AD DS e AD LDS' na página Funcionalidades](./media/tutorial-create-management-vm/install-features.png)

1. Na página **confirmação,** selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas administrativas.
1. Quando a instalação da funcionalidade estiver concluída, selecione **Close** to exit the **Add Roles and Features** wizard.

## <a name="use-active-directory-administrative-tools"></a>Utilize ferramentas administrativas de Diretório Ativo

Com as ferramentas administrativas instaladas, vamos ver como usá-las para administrar o domínio gerido pelo Azure AD DS. Certifique-se de que está inscrito no VM com uma conta de utilizador que é membro do grupo de administradores da *AAD DC.*

1. A partir do menu **Iniciar,** selecione **Ferramentas Administrativas windows**. As ferramentas administrativas da AD instaladas no escalão anterior estão listadas.

    ![Lista de Ferramentas Administrativas instaladas no servidor](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Selecione **Ative Directory Administrative Center**.
1. Para explorar o domínio gerido pelo Azure AD DS, escolha o nome de domínio no painel esquerdo, como *aaddscontoso.com*. Dois contentores chamados *AADDC Computers* e *Utilizadores AADDC* estão no topo da lista.

    ![Enumerar os contentores disponíveis do domínio gerido pela AD DS azure](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Para ver os utilizadores e grupos que pertencem ao domínio gerido pelo Azure AD DS, selecione o recipiente **Utilizadores AADDC.** As contas de utilizador e grupos do seu inquilino Azure AD estão listados neste contentor.

    Na saída de exemplo seguinte, uma conta de utilizador chamada *Contoso Admin* e um grupo de *Administradores aAD DC* são mostrados neste recipiente.

    ![Ver a lista de utilizadores de domínio Azure AD DS no Ative Directory Administrative Center](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Para ver os computadores que estão ligados ao domínio gerido pelo Azure AD DS, selecione o recipiente **AADDC Computers.** Está listada uma entrada para a atual máquina virtual, como o *myVM.* As contas de computador para todos os computadores que estejam ligados ao domínio gerido pelo Azure AD DS são armazenadas neste recipiente *de Computadores AADDC.*

Estão disponíveis ações do Common Ative Directory Administrative Center, tais como a reposição de uma palavra-passe de conta de utilizador ou a gestão da adesão ao grupo. Estas ações funcionam apenas para utilizadores e grupos criados diretamente no domínio gerido pelo Azure AD DS. A informação de identidade apenas sincroniza *de* Azure AD para Azure AD DS. Não há nenhuma redação do Azure AD DS para a Azure AD. Não é possível alterar palavras-passe ou membros de grupo geridos para utilizadores sincronizados a partir de Azure AD e ter essas alterações sincronizadas.

Também pode utilizar o *Módulo de Diretório Ativo para windows PowerShell,* instalado como parte das ferramentas administrativas, para gerir ações comuns no seu domínio gerido pelo Azure AD DS.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Compreender as tarefas administrativas disponíveis num domínio gerido pela AD DS azure
> * Instale as ferramentas administrativas do Diretório Ativo num VM do Servidor windows
> * Utilize o Centro Administrativo de Diretório Ativo para executar tarefas comuns

Para interagir com segurança com o seu domínio gerido pelo Azure AD DS, ative o Protocolo de Acesso ao Diretório Leve seguro (LDAPS).

> [!div class="nextstepaction"]
> [Configure lDAP seguro para o seu domínio gerido](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
