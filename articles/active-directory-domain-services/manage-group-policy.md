---
title: 'Azure Active Directory Domain Services: Administrar Política de Grupo | Microsoft Docs'
description: Administrar o Política de Grupo em Azure Active Directory Domain Services domínios gerenciados
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
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: c7b32885fdb3cf4f3e584c916d6b234fff54bfc4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234033"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrar Política de Grupo em um domínio Azure AD Domain Services gerenciado
Azure Active Directory Domain Services inclui objetos de Política de Grupo (GPOs) internos para os contêineres ' AADDC users ' e ' AADDC Computers '. Você pode personalizar esses GPOs internos para configurar Política de Grupo no domínio gerenciado. Além disso, os membros do grupo "administradores do controlador de domínio do AAD" podem criar suas próprias UOs personalizadas no domínio gerenciado. Eles também podem criar GPOs personalizados e vinculá-los a essas UOs personalizadas. Os usuários que pertencem ao grupo "administradores do AAD DC" recebem privilégios de administração Política de Grupo no domínio gerenciado.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisará de:

1. Uma **assinatura válida do Azure**.
2. Um **diretório do Azure ad** -seja sincronizado com um diretório local ou um diretório somente na nuvem.
3. **Azure AD Domain Services** deve ser habilitado para o diretório do Azure AD. Se você ainda não fez isso, siga todas as tarefas descritas no [Guia de introdução](create-instance.md).
4. Uma **máquina virtual ingressada no domínio** da qual você administra o Azure AD Domain Services domínio gerenciado. Se você não tiver essa máquina virtual, siga todas as tarefas descritas no artigo [ingressar uma máquina virtual do Windows em um domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).
5. Você precisa das credenciais de uma **conta de usuário que pertença ao grupo ' Administradores do controlador de domínio do AAD '** em seu diretório, para administrar política de grupo para seu domínio gerenciado.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Tarefa 1-provisionar uma máquina virtual ingressada no domínio para administrar remotamente Política de Grupo para o domínio gerenciado
Azure AD Domain Services domínios gerenciados podem ser gerenciados remotamente usando ferramentas administrativas Active Directory familiares, como o Centro Administrativo do Active Directory (ADAC) ou o AD PowerShell. Da mesma forma, Política de Grupo para o domínio gerenciado pode ser administrado remotamente usando as ferramentas de administração de Política de Grupo.

Os administradores do seu diretório do Azure AD não têm privilégios para se conectar aos controladores de domínio no domínio gerenciado por meio do Área de Trabalho Remota. Os membros do grupo ' Administradores de controlador de domínio do AAD ' podem administrar Política de Grupo remotamente para domínios gerenciados. Eles podem usar as ferramentas de Política de Grupo em um computador cliente/servidor do Windows ingressado no domínio gerenciado. Política de Grupo ferramentas podem ser instaladas como parte do recurso opcional de gerenciamento de Política de Grupo no Windows Server e computadores cliente ingressados no domínio gerenciado.

A primeira tarefa é provisionar uma máquina virtual do Windows Server que tenha ingressado no domínio gerenciado. Para obter instruções, consulte o artigo intitulada [unir uma máquina virtual do Windows Server a um Azure AD Domain Services domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Tarefa 2-instalar as ferramentas de Política de Grupo na máquina virtual
Execute as etapas a seguir para instalar as ferramentas de administração de Política de Grupo na máquina virtual ingressada no domínio.

1. Navegue até a portal do Azure. Clique em **todos os recursos** no painel esquerdo. Localize e clique na máquina virtual que você criou na tarefa 1.
2. Clique no botão **conectar** na guia Visão geral. Um arquivo protocolo RDP (. RDP) é criado e baixado.

    ![Conectar-se à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. No prompt de logon, use as credenciais de um usuário que pertence ao grupo "administradores do controlador de domínio do AAD". Por exemplo, usamos 'bob@domainservicespreview.onmicrosoft.com' em nosso caso. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em Sim ou continuar para prosseguir com a conexão.
4. Na tela iniciar, abra **Gerenciador do servidor**. Clique em **adicionar funções e recursos** no painel central da janela de Gerenciador do servidor.

    ![Iniciar Gerenciador do Servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na página **antes de começar** do **Assistente para adicionar funções e recursos**, clique em **Avançar**.

    ![Antes de começar a página](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na página **tipo de instalação** , deixe a opção de instalação baseada em **função ou recurso** marcada e clique em **Avançar**.

    ![Página tipo de instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na página **seleção de servidor** , selecione a máquina virtual atual no pool de servidores e clique em **Avançar**.

    ![Página de seleção de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na página **funções de servidor** , clique em **Avançar**. Ignoramos esta página, pois não estamos instalando nenhuma função no servidor.
9. Na página **recursos** , selecione o recurso de **Gerenciamento de política de grupo** .

    ![Página de recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Na página **confirmação** , clique em **instalar** para instalar o recurso de gerenciamento de política de grupo na máquina virtual. Quando a instalação do recurso for concluída com êxito, clique em **fechar** para sair do assistente **adicionar funções e recursos** .

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Tarefa 3-iniciar o console de gerenciamento de Política de Grupo para administrar Política de Grupo
Você pode usar o console de gerenciamento do Política de Grupo na máquina virtual ingressada no domínio para administrar Política de Grupo no domínio gerenciado.

> [!NOTE]
> Você precisa ser um membro do grupo "administradores do controlador de domínio do AAD" para administrar Política de Grupo no domínio gerenciado.
>
>

1. Na tela iniciar, clique em **Ferramentas administrativas**. Você deve ver o console de **Gerenciamento do política de grupo** instalado na máquina virtual.

    ![Iniciar o gerenciamento de Política de Grupo](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Clique em **Gerenciamento de política de grupo** para iniciar o console de gerenciamento de política de grupo.

    ![Console do Política de Grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Tarefa 4-personalizar objetos de Política de Grupo internos
Há dois objetos de Política de Grupo internos (GPOs), um para os contêineres ' AADDC Computers ' e ' AADDC users ' em seu domínio gerenciado. Você pode personalizar esses GPOs para configurar a política de grupo no domínio gerenciado.

1. No console de **Gerenciamento do política de grupo** , clique para expandir os nós **floresta: contoso100.com** e **domínios** para ver as políticas de grupo para seu domínio gerenciado.

    ![GPOs internos](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Você pode personalizar esses GPOs internos para configurar políticas de grupo em seu domínio gerenciado. Clique com o botão direito do mouse no GPO e clique em **Editar...** para personalizar o GPO interno. A ferramenta do editor de configuração do Política de Grupo permite que você personalize o GPO.

    ![Editar GPO interno](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Agora você pode usar o console do **Editor de gerenciamento de política de grupo** para editar o GPO interno. Por exemplo, a captura de tela a seguir mostra como personalizar o GPO interno de ' AADDC Computers '.

    ![Personalizar GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Tarefa 5 – criar um objeto de Política de Grupo personalizado (GPO)
Você pode criar ou importar seus próprios objetos de política de grupo personalizados. Você também pode vincular GPOs personalizados a uma UO personalizada que você criou em seu domínio gerenciado. Para obter mais informações sobre como criar unidades organizacionais personalizadas, consulte [criar uma UO personalizada em um domínio gerenciado](create-ou.md).

> [!NOTE]
> Você precisa ser um membro do grupo "administradores do controlador de domínio do AAD" para administrar Política de Grupo no domínio gerenciado.
>
>

1. No console de **Gerenciamento do política de grupo** , clique para selecionar sua UO (unidade organizacional) personalizada. Clique com o botão direito do mouse na UO e clique em **criar um GPO nesse domínio e vincule-o aqui...** .

    ![Criar um GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Especifique um nome para o novo GPO e clique em **OK**.

    ![Especifique um nome para o GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Um novo GPO é criado e vinculado à sua UO personalizada. Clique com o botão direito do mouse no GPO e clique em **Editar...** no menu.

    ![GPO recém-criado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Você pode personalizar o GPO recém-criado usando o **Editor de gerenciamento de política de grupo**.

    ![Personalizar novo GPO](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Mais informações sobre como usar o [console de gerenciamento de política de grupo](https://technet.microsoft.com/library/cc753298.aspx) estão disponíveis no TechNet.

## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um Azure AD Domain Services domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Gerenciar um domínio de Azure AD Domain Services](manage-domain.md)
* [Console de Gerenciamento de Política de Grupo](https://technet.microsoft.com/library/cc753298.aspx)
