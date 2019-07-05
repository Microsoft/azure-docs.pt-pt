---
title: Gerir o DNS para serviços de domínio do Azure AD | Documentos da Microsoft
description: Gerir o DNS para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 6753c26a99bb38e92613a6bad753e7dd101ba68e
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473132"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrar DNS num domínio gerido do Azure AD Domain Services
Os serviços de domínio do Active Directory do Azure inclui um servidor DNS (resolução de nomes de domínio) que fornece resolução de DNS para o domínio gerido. Ocasionalmente, terá de configurar o DNS no domínio gerido. Se pretender criar registos DNS para as máquinas que não estão associados ao domínio, configure os endereços IP virtuais para balanceadores de carga ou programa de configuração de reencaminhadores DNS externos. Por esse motivo, os utilizadores que pertencem ao grupo "Administradores do AAD DC" são concedidos privilégios de administração do DNS no domínio gerido.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para concluir as tarefas apresentadas neste artigo, precisa:

1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](create-instance.md).
4. R **associados a um domínio máquina de virtual** partir do qual administra o domínio gerido do Azure AD Domain Services. Se não tiver um computador virtual, siga todas as tarefas descritas no artigo intitulado [associar uma máquina de virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).
5. Terá das credenciais de uma **conta de utilizador que pertencem ao grupo "Administradores do AAD DC"** no seu diretório, para administrar o DNS para o seu domínio gerido.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tarefa 1 - criar uma máquina de virtual associado a um domínio para administrar remotamente o DNS para o domínio gerido
Domínios de geridos de serviços de domínio do AD do Azure podem ser geridos remotamente utilizando o Active Directory administrativas ferramentas familiares como o Active Directory Centro de administração (ADAC) ou o AD PowerShell. Da mesma forma, o DNS para o domínio gerido pode ser administrado remotamente com as ferramentas de administração do servidor DNS.

Os administradores no seu diretório do Azure AD não tem privilégios para ligar aos controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Membros do grupo "Administradores do AAD DC" podem administrar o DNS para domínios geridos remotamente utilizando ferramentas de servidor DNS de um computador de servidor/cliente Windows que está associado ao domínio gerido. Ferramentas de servidor DNS fazem parte da funcionalidade ferramentas de administração remota de servidor (FARS) opcional.

A primeira tarefa é criar uma máquina virtual do Windows Server que está associada ao domínio gerido. Para obter instruções, consulte o artigo intitulado [associar uma máquina de virtual do Windows Server a um domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tarefa 2 – ferramentas de servidor de DNS de instalação na máquina virtual
Conclua os seguintes passos para instalar as ferramentas de administração de DNS na máquina de virtual associado a um domínio. Para obter mais informações sobre [instalar e utilizar ferramentas de administração remota do servidor](https://technet.microsoft.com/library/hh831501.aspx), consulte o Technet.

1. Navegue para o portal do Azure. Clique em **todos os recursos** no painel à esquerda. Localize e clique na máquina virtual que criou na tarefa 1.
2. Clique nas **Connect** botão na guia visão geral. Um ficheiro do protocolo de ambiente de trabalho remoto (. rdp) é criado e transferido.

    ![Ligar à máquina de virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Utilize as credenciais de um usuário pertencente ao grupo "Administradores do AAD DC". Por exemplo, "bob@domainservicespreview.onmicrosoft.com'. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em Sim ou continuar e ligar.

4. A partir do ecrã Iniciar, abra **Gestor de servidor**. Clique em **para adicionar funções e funcionalidades** no painel central da janela do Gestor de servidores.

    ![Inicie o Gestor de servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Sobre o **antes de começar** página do **Assistente Adicionar funções e funcionalidades**, clique em **seguinte**.

    ![Antes de iniciar a página](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Sobre o **tipo de instalação** página, deixe a **instalação baseada em funções ou baseada em recursos** opção marcada e clique em **seguinte**.

    ![Página tipo de instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Sobre o **seleção de servidor** página, selecione a máquina virtual atual no agrupamento de servidores e clique em **próxima**.

    ![Página de seleção de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Sobre o **funções de servidor** página, clique em **próxima**.
9. Na **funcionalidades** página, clique para expandir o **ferramentas de administração remota do servidor de** nó e, em seguida, clique para expandir o **ferramentas de administração de função** nó. Selecione **ferramentas do servidor DNS** funcionalidade a partir da lista de ferramentas de administração de função.

    ![Página de recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Sobre o **confirmação** página, clique em **instalar** para instalar a funcionalidade de ferramentas do servidor DNS na máquina virtual. Quando a instalação da funcionalidade for concluída com êxito, clique em **fechar** para sair do **para adicionar funções e funcionalidades** assistente.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tarefa 3 - inicie a consola de gestão de DNS para administrar o DNS
Agora, pode utilizar ferramentas de DNS do Windows Server para administrar o DNS no domínio gerido.

> [!NOTE]
> Tem de ser membro do grupo "Administradores do AAD DC", para administrar o DNS no domínio gerido.
>
>

1. No ecrã Iniciar, clique em **ferramentas administrativas**. Deverá ver o **DNS** consola instalada na máquina virtual.

    ![Ferramentas administrativas - consola DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Clique em **DNS** para iniciar o console de gerenciamento do DNS.
3. Na **ligar ao servidor DNS** caixa de diálogo, clique em **o seguinte computador**e introduza o nome de domínio DNS do domínio gerido (por exemplo, ' contoso100.com').

    ![Consola DNS - ligar ao domínio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. A consola DNS liga-se ao domínio gerido.

    ![Consola DNS - administrar domínio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Agora, pode utilizar a consola DNS para adicionar entradas DNS para computadores na rede virtual em que tiver ativado o serviços de domínio do AAD.

> [!WARNING]
> Tenha cuidado ao administrar o DNS para o domínio gerido com as ferramentas de administração do DNS. Certifique-se de que **não eliminar ou modificar os registos DNS internos que são utilizados pelos serviços de domínio no domínio**. Registos DNS internos incluem registos DNS de domínio, os registos de servidor de nomes e outros registos utilizados para a localização do controlador de domínio. Se modificar estes registos, os serviços de domínio são interrompidos na rede virtual.
>
>

Para obter mais informações sobre a gestão de DNS, consulte a [Ferramentas DNS artigo no Technet](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](create-instance.md)
* [Junte-se a uma máquina virtual do Windows Server a um domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Gerir um domínio do Azure AD Domain Services](manage-domain.md)
* [Ferramentas de administração do DNS](https://technet.microsoft.com/library/cc753579.aspx)
