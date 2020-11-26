---
title: Tutorial - Criar um VM de gestão para serviços de domínio de diretório ativo Azure / Microsoft Docs
description: Neste tutorial, aprende-se a criar e configurar uma máquina virtual Windows que utiliza para administrar o domínio gerido pelo Azure Ative Directory Domain Services.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 52a97b824824d8e9eaf79cfa4a447494bf1525cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175156"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Criar um VM de gestão para configurar e administrar um domínio gerido por Serviços de Domínio do Diretório Ativo Azure

Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a junção de domínio, a política de grupo, a autenticação LDAP e a autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server Ative Directory. Administra este domínio gerido utilizando as mesmas Ferramentas de Administração de ServidorEs Remotos (RSAT) que com um domínio de Serviços de Domínio de Diretório Ativo no local. Como o Azure AD DS é um serviço gerido, existem algumas tarefas administrativas que não pode executar, como a utilização do protocolo de ambiente de trabalho remoto (RDP) para ligar aos controladores de domínio.

Este tutorial mostra-lhe como configurar um VM do Servidor do Windows em Azure e instalar as ferramentas necessárias para administrar um domínio gerido pelo Azure AD DS.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Compreender as tarefas administrativas disponíveis num domínio gerido
> * Instale as ferramentas administrativas do Ative Directory num VM do Servidor do Windows
> * Utilize o Ative Directory Administrative Center para executar tarefas comuns

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, consulte o primeiro tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Um VM do Servidor do Windows que se junta ao domínio gerido.
    * Se necessário, consulte o tutorial anterior para [criar um VM do Servidor do Windows e junte-o a um domínio gerido][create-join-windows-vm].
* Uma conta de utilizador que é membro do grupo de administradores da *Ad DC Azure* no seu inquilino AZure AD.
* Um anfitrião Azure Bastion implantado na sua rede virtual Azure AD DS.
    * Se necessário, [crie um anfitrião Azure Bastion][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria-se e configura um VM de gestão utilizando o portal Azure. Para começar, inicie primeiro o sinal no [portal Azure.](https://portal.azure.com)

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Tarefas administrativas disponíveis em Azure AD DS

O Azure AD DS fornece um domínio gerido para os seus utilizadores, aplicações e serviços para consumir. Esta abordagem altera algumas das tarefas de gestão disponíveis que pode fazer e que privilégios tem dentro do domínio gerido. Estas tarefas e permissões podem ser diferentes das que experimenta com um ambiente regular de Serviços de Domínio de Diretório Ativo no local. Também não é possível ligar-se a controladores de domínio no domínio gerido utilizando o Ambiente de Trabalho Remoto.

### <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tarefas administrativas que pode executar num domínio gerido

Os membros do grupo *de administradores da AAD DC* recebem privilégios no domínio gerido que lhes permite fazer tarefas como:

* Configure o objeto de política de grupo incorporado (GPO) para os recipientes *de computadores AADDC* e *utilizadores AADDC* no domínio gerido.
* Administrar o DNS no domínio gerido.
* Criar e administrar unidades organizacionais personalizadas (OUs) no domínio gerido.
* Obter acesso administrativo aos computadores associados ao domínio gerido.

### <a name="administrative-privileges-you-dont-have-on-a-managed-domain"></a>Privilégios administrativos que não tem num domínio gerido

O domínio gerido está bloqueado, por isso não tem privilégios para fazer certas tarefas administrativas no domínio. Alguns dos seguintes exemplos são tarefas que não pode fazer:

* Alargar o esquema do domínio gerido.
* Ligue-se aos controladores de domínio para o domínio gerido utilizando o Ambiente de Trabalho Remoto.
* Adicione controladores de domínio ao domínio gerido.
* Não tem privilégios *de Administrador de Domínio* ou Administrador *Empresarial* para o domínio gerido.

## <a name="sign-in-to-the-windows-server-vm"></a>Iniciar serção no Windows Server VM

No tutorial anterior, foi criado um VM do Windows Server e aderido ao domínio gerido. Utilize este VM para instalar as ferramentas de gestão. Se necessário, [siga os passos do tutorial para criar e juntar um VM do Windows Server a um domínio gerido][create-join-windows-vm].

> [!NOTE]
> Neste tutorial, utiliza-se um VM do Windows Server em Azure que se junta ao domínio gerido. Também pode utilizar um cliente Windows, como o Windows 10, que se junta ao domínio gerido.
>
> Para obter mais informações sobre como instalar as ferramentas administrativas num cliente Windows, consulte [instalar Ferramentas de Administração de Servidor Remoto (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Para começar, ligue-se ao VM do Servidor do Windows da seguinte forma:

1. No portal Azure, selecione **grupos de recursos** do lado esquerdo. Escolha o grupo de recursos onde o seu VM foi criado, como *o myResourceGroup,* e selecione o VM, como *o myVM*.
1. No **painel de visão geral** para o seu VM, selecione **Connect**, em **seguida, Bastion**.

    ![Ligue à máquina virtual do Windows utilizando o Bastion no portal Azure](./media/join-windows-vm/connect-to-vm.png)

1. Introduza as credenciais para o seu VM e, em seguida, selecione **Connect**.

   ![Conecte-se através do hospedeiro Bastião no portal Azure](./media/join-windows-vm/connect-to-bastion.png)

Se necessário, permita que o seu navegador da Web abra pop-ups para a ligação Bastion ser exibida. Demora alguns segundos a fazer a ligação ao seu VM.

## <a name="install-active-directory-administrative-tools"></a>Instalar ferramentas administrativas do Diretório Ativo

Utiliza as mesmas ferramentas administrativas num domínio gerido como ambientes AD DS no local, como o Ative Directory Administrative Center (ADAC) ou o AD PowerShell. Estas ferramentas podem ser instaladas como parte da funcionalidade Ferramentas de Administração de Servidor Remoto (RSAT) no Windows Server e nos computadores clientes. Os membros do grupo *de administradores AAD DC* podem então administrar domínios geridos remotamente usando estas ferramentas administrativas de AD a partir de um computador que é associado ao domínio gerido.

Para instalar as ferramentas da Administração do Diretório Ativo num VM ligado ao domínio, complete os seguintes passos:

1. Se **o Gestor do Servidor** não abrir por predefinição quando iniciar serção no VM, selecione o menu **Iniciar** e, em seguida, escolha o Gestor **do Servidor**.
1. No *painel de instrumentos* da janela **'Gestor do servidor',** selecione **Adicionar Funções e Funcionalidades**.
1. Na **página antes** de começar a página do Assistente de *Funções e Funcionalidades adicionais*, selecione **Seguinte**.
1. Para o *Tipo de Instalação*, deixe a opção **de instalação baseada em funções ou baseada em recursos** verificada e selecione **Seguinte**.
1. Na página **'Seleção** do Servidor', escolha o VM atual a partir da piscina do servidor, como *myvm.aaddscontoso.com,* selecione **Next**.
1. Na página **'Funções do Servidor',** clique em **'Seguinte'.**
1. Na página **Funcionalidades,** expanda o nó **de Ferramentas de Administração do Servidor Remoto** e, em seguida, expanda o nó **ferramentas de administração** de funções.

    Escolha a função **AD DS e AD LDS Tools** na lista de ferramentas de administração de funções e, em seguida, selecione **Next**.

    ![Instale as 'Ferramentas AD DS e AD LDS' a partir da página 'Funcionalidades'](./media/tutorial-create-management-vm/install-features.png)

1. Na página **De Confirmação,** selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas administrativas.
1. Quando a instalação da funcionalidade estiver concluída, selecione **Close** to exit the **Add Roles and Features** wizard.

## <a name="use-active-directory-administrative-tools"></a>Utilizar ferramentas administrativas do Diretório Ativo

Com as ferramentas administrativas instaladas, vamos ver como usá-las para administrar o domínio gerido. Certifique-se de que está inscrito no VM com uma conta de utilizador que é membro do grupo de administradores da *AAD DC.*

1. A partir do menu **Iniciar,** selecione **Ferramentas Administrativas do Windows**. As ferramentas administrativas AD instaladas no passo anterior estão listadas.

    ![Lista de Ferramentas Administrativas instaladas no servidor](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Selecione **Ative Directory Administrative Center**.
1. Para explorar o domínio gerido, escolha o nome de domínio no painel esquerdo, como *aaddscontoso*. Dois contentores chamados *AADDC Computers* e *AADDC Users* estão no topo da lista.

    ![Listar os contentores disponíveis parte do domínio gerido](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Para ver os utilizadores e grupos que pertencem ao domínio gerido, selecione o recipiente **Utilizadores AADDC.** As contas de utilizador e grupos do seu inquilino Azure AD estão listados neste recipiente.

    No seguinte exemplo, uma conta de utilizador chamada *Contoso Admin* e um grupo de *Administradores AAD DC* são mostrados neste recipiente.

    ![Veja a lista de utilizadores de domínio Azure AD DS no Ative Directory Administrative Center](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Para ver os computadores que estão unidos ao domínio gerido, selecione o contentor **AADDC Computers.** Está listada uma entrada para a atual máquina virtual, como *o myVM.* As contas do computador de todos os dispositivos que se unem ao domínio gerido são armazenadas neste contentor *de Computadores AADDC.*

Estão disponíveis ações do Common Ative Directory Administrative Center, tais como a reposição de uma palavra-passe da conta de utilizador ou a gestão da adesão ao grupo. Estas ações apenas funcionam para utilizadores e grupos criados diretamente no domínio gerido. A informação de identidade apenas sincroniza *de* Azure AD a Azure AD DS. Não há nenhuma escrita de Azure AD DS para Azure AD. Não é possível alterar palavras-passe ou membros geridos do grupo para utilizadores sincronizados a partir do Azure AD e ter essas alterações sincronizadas.

Também pode utilizar o *Módulo de Diretório Ativo para Windows PowerShell,* instalado como parte das ferramentas administrativas, para gerir ações comuns no seu domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Compreender as tarefas administrativas disponíveis num domínio gerido
> * Instale as ferramentas administrativas do Ative Directory num VM do Servidor do Windows
> * Utilize o Ative Directory Administrative Center para executar tarefas comuns

Para interagir com segurança com o seu domínio gerido a partir de outras aplicações, ative um Protocolo de Acesso leve (LDAPS).

> [!div class="nextstepaction"]
> [Configure lDAP seguro seguro para o seu domínio gerido](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md