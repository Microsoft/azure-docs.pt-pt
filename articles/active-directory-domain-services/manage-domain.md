---
title: 'Azure Active Directory Domain Services: Administrar um domínio gerido | Documentos da Microsoft'
description: Administrar domínios geridos do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4095161cd35ff610b357196c91e71256a8bc0263
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473015"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrar um domínio gerido pelos Serviços de Domínio do Azure Active Directory
Este artigo mostra-lhe como administrar um domínio gerido dos serviços de domínio do Azure Active Directory (AD).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para concluir as tarefas apresentadas neste artigo, precisa:

1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](create-instance.md).
4. R **associados a um domínio máquina de virtual** partir do qual administra o domínio gerido do Azure AD Domain Services. Se não tiver um computador virtual, siga todas as tarefas descritas no artigo intitulado [associar uma máquina de virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).
5. Terá das credenciais de uma **conta de utilizador que pertencem ao grupo "Administradores do AAD DC"** no seu diretório, para administrar o seu domínio gerido.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tarefas administrativas, que pode executar num domínio gerido
Membros do grupo "Administradores do AAD DC" são concedidos privilégios no domínio gerido, que permitem a elas realizar tarefas como:

* Associar máquinas ao domínio gerido.
* Configurar o GPO incorporado para os contentores “Computadores do AAD DC” e “Utilizadores do AAD DC” no domínio gerido.
* Administrar o DNS no domínio gerido.
* Criar e administrar personalizado unidades organizacionais (UOs) no domínio gerido.
* Obter acesso administrativo aos computadores associados ao domínio gerido.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Privilégios administrativos que não tem num domínio gerido
O domínio é gerido pela Microsoft, incluindo atividades, como a aplicação de patches, monitorização e, fazer cópias de segurança. O domínio é bloqueado e não tem privilégios para realizarem determinadas tarefas administrativas no domínio. Alguns exemplos de tarefas que não é possível fazer são abaixo.

* Não tem privilégios de administrador Corporativo ou de administrador de domínio para o domínio gerido.
* Não é possível expandir o esquema do domínio gerido.
* É possível ligar a controladores de domínio para o domínio gerido com o ambiente de trabalho remoto.
* Não é possível adicionar controladores de domínio para o domínio gerido.

## <a name="task-1---create-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tarefa 1 - criar uma máquina de virtual associado a um domínio do Windows Server para administrar remotamente o domínio gerido
Domínios de geridos de serviços de domínio do AD do Azure podem ser geridos através do Active Directory administrativas ferramentas familiares como o Active Directory Centro de administração (ADAC) ou o AD PowerShell. Os administradores de inquilinos não tem privilégios para ligar aos controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Membros do grupo "Administradores do AAD DC" podem administrar os domínios geridos remotamente utilizando ferramentas administrativas do AD de um computador de servidor/cliente Windows que está associado ao domínio gerido. Ferramentas administrativas do AD podem ser instaladas como parte da funcionalidade ferramentas de administração remota de servidor (FARS) opcional no Windows Server e máquinas de cliente associadas ao domínio gerido.

A primeira etapa é configurar uma máquina virtual do Windows Server que está associada ao domínio gerido. Para obter instruções, consulte o artigo intitulado [associar uma máquina de virtual do Windows Server a um domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrar domínio gerido remotamente a partir de um computador cliente (por exemplo, o Windows 10)
As instruções neste uso de artigo uma máquina virtual do Windows Server para administrar o AAD-DS o domínio gerido. No entanto, também pode optar por usar uma máquina de virtual de cliente (por exemplo, o Windows 10) do Windows para fazer isso.

Pode [instalar ferramentas de administração remota de servidor (FARS)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) numa máquina de virtual de cliente do Windows ao seguir as instruções no TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tarefa 2 – ferramentas de administração do Active Directory da instalação na máquina virtual
Conclua os seguintes passos para instalar as ferramentas de administração do Active Directory na máquina de virtual associado a um domínio. Consulte o Technet para obter mais informações [informações sobre como instalar e utilizar ferramentas de administração remota do servidor](https://technet.microsoft.com/library/hh831501.aspx).

1. Navegue para o portal do Azure. Clique em **todos os recursos** no painel à esquerda. Localize e clique na máquina virtual que criou na tarefa 1.
2. Clique nas **Connect** botão na guia visão geral. Um ficheiro do protocolo de ambiente de trabalho remoto (. rdp) é criado e transferido.

    ![Ligar à máquina de virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Utilize as credenciais de um usuário pertencente ao grupo "Administradores do AAD DC". Por exemplo, "bob@domainservicespreview.onmicrosoft.com'. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em Sim ou continuar a prosseguir com a ligação.
4. A partir do ecrã Iniciar, abra **Gestor de servidor**. Clique em **para adicionar funções e funcionalidades** no painel central da janela do Gestor de servidores.

    ![Inicie o Gestor de servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Sobre o **antes de começar** página do **Assistente Adicionar funções e funcionalidades**, clique em **seguinte**.

    ![Antes de iniciar a página](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Sobre o **tipo de instalação** página, deixe a **instalação baseada em funções ou baseada em recursos** opção marcada e clique em **seguinte**.

    ![Página tipo de instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Sobre o **seleção de servidor** página, selecione a máquina virtual atual no agrupamento de servidores e clique em **próxima**.

    ![Página de seleção de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Sobre o **funções de servidor** página, clique em **próxima**.
9. Na **funcionalidades** página, clique para expandir o **ferramentas de administração remota do servidor de** nó e, em seguida, clique para expandir o **ferramentas de administração de função** nó. Selecione **AD DS e AD LDS ferramentas** funcionalidade a partir da lista de ferramentas de administração de função.

    ![Página de recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Sobre o **confirmação** página, clique em **instalar** para instalar o AD e a funcionalidade de ferramentas do AD LDS na máquina virtual. Quando a instalação da funcionalidade for concluída com êxito, clique em **fechar** para sair do **para adicionar funções e funcionalidades** assistente.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tarefa 3 - ligar e explorar o domínio gerido
Agora, pode usar ferramentas administrativas do Windows Server AD para explorar e administrar domínio gerido.

> [!NOTE]
> Tem de ser um membro do grupo "Administradores do AAD DC", para administrar o domínio gerido.
>
>

1. No ecrã Iniciar, clique em **ferramentas administrativas**. Deverá ver as ferramentas administrativas do AD instaladas na máquina virtual.

    ![Ferramentas administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Clique em **Centro de administração do Active Directory**.

    ![Centro de administração do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Para explorar o domínio, clique no nome de domínio no painel da esquerda (por exemplo, ' contoso100.com'). Observe os dois contentores chamados "Computadores do aad DC" e "Utilizadores do aad DC", respectivamente.

    ![ADAC - domínio de vista](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Clique em contentor denominado **utilizadores do aad DC** para ver todos os utilizadores e grupos que pertençam ao domínio gerido. Deverá ver as contas de utilizador e grupos do Azure AD de inquilinos show cópia de segurança neste contentor. Observe que, neste exemplo, uma conta de utilizador para o utilizador chamado "bob" e um grupo chamado "Administradores do AAD DC" estão disponíveis neste contentor.

    ![ADAC - utilizadores de domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Clique em contentor denominado **computadores do aad DC** para ver os computadores associados a este domínio gerido. Deverá ver uma entrada para a máquina virtual atual, que é associada ao domínio. Contas de computador para todos os computadores que estão associados ao domínio gerido do Azure AD Domain Services são armazenadas neste contentor de "Computadores do aad DC".

    ![ADAC - computadores associados ao domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](create-instance.md)
* [Junte-se a uma máquina virtual do Windows Server a um domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Implementar ferramentas de administração remota do servidor](https://technet.microsoft.com/library/hh831501.aspx)
