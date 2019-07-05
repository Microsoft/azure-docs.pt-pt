---
title: 'Azure Active Directory Domain Services: Administrar política de grupo em domínios geridos | Documentos da Microsoft'
description: Domínios de geridos de política de grupo de administrar no Azure Active Directory Domain Services
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
ms.openlocfilehash: 3776ec6fd60ad888102cbc4992572516386fede2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473103"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrar política de grupo num domínio gerido do Azure AD Domain Services
Os serviços de domínio do Active Directory do Azure inclui objetos de política de grupo (GPOs) incorporada para os contentores de "Utilizadores do aad DC" e "Computadores do aad DC". Pode personalizar estes GPOs incorporados para configurar a política de grupo no domínio gerido. Além disso, os membros do grupo "Administradores do AAD DC" podem criar suas próprias unidades organizacionais personalizados no domínio gerido. Também pode criar GPOs personalizados e vinculá-las para essas UOs personalizadas. Os utilizadores que pertencem ao grupo "Administradores do AAD DC" são concedidos privilégios de administração de política de grupo no domínio gerido.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, terá de:

1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](create-instance.md).
4. R **associados a um domínio máquina de virtual** partir do qual administra o domínio gerido do Azure AD Domain Services. Se não tiver um computador virtual, siga todas as tarefas descritas no artigo intitulado [associar uma máquina de virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).
5. Terá das credenciais de uma **conta de utilizador que pertencem ao grupo "Administradores do AAD DC"** no seu diretório, para administrar a política de grupo para o seu domínio gerido.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Tarefa 1 - aprovisionar uma máquina de virtual associado a um domínio para administrar remotamente a diretiva de grupo para o domínio gerido
Domínios de geridos de serviços de domínio do AD do Azure podem ser geridos remotamente utilizando o Active Directory administrativas ferramentas familiares como o Active Directory Centro de administração (ADAC) ou o AD PowerShell. Da mesma forma, a política de grupo para o domínio gerido pode ser administrada remotamente com as ferramentas de administração da diretiva de grupo.

Os administradores no seu diretório do Azure AD não tem privilégios para ligar aos controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Membros do grupo "Administradores do AAD DC" podem administrar política de grupo para domínios geridos remotamente. Eles podem usar ferramentas de diretiva de grupo num computador Windows Server/cliente associado ao domínio gerido. Ferramentas da política de grupo podem ser instaladas como parte da funcionalidade opcional do gerenciamento de diretiva de grupo, no Windows Server e máquinas de cliente associadas ao domínio gerido.

A primeira tarefa é aprovisionar uma máquina virtual do Windows Server que está associada ao domínio gerido. Para obter instruções, consulte o artigo intitulado [associar uma máquina de virtual do Windows Server a um domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Tarefa 2 - ferramentas de diretiva de grupo de instalação na máquina virtual
Execute os seguintes passos para instalar as ferramentas de administração da diretiva de grupo na máquina de virtual associado a um domínio.

1. Navegue para o portal do Azure. Clique em **todos os recursos** no painel à esquerda. Localize e clique na máquina virtual que criou na tarefa 1.
2. Clique nas **Connect** botão na guia visão geral. Um ficheiro do protocolo de ambiente de trabalho remoto (. rdp) é criado e transferido.

    ![Ligar à máquina de virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. No prompt de início de sessão, utilize as credenciais de um usuário pertencente ao grupo "Administradores do AAD DC". Por exemplo, podemos usar 'bob@domainservicespreview.onmicrosoft.com' no nosso caso. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em Sim ou continuar a prosseguir com a ligação.
4. A partir do ecrã Iniciar, abra **Gestor de servidor**. Clique em **para adicionar funções e funcionalidades** no painel central da janela do Gestor de servidores.

    ![Inicie o Gestor de servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Sobre o **antes de começar** página do **Assistente Adicionar funções e funcionalidades**, clique em **seguinte**.

    ![Antes de iniciar a página](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Sobre o **tipo de instalação** página, deixe a **instalação baseada em funções ou baseada em recursos** opção marcada e clique em **seguinte**.

    ![Página tipo de instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Sobre o **seleção de servidor** página, selecione a máquina virtual atual no agrupamento de servidores e clique em **próxima**.

    ![Página de seleção de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Sobre o **funções de servidor** página, clique em **próxima**. Vamos ignorar esta página, uma vez que estamos não estiver a instalar quaisquer funções no servidor.
9. Sobre o **funcionalidades** página, selecione o **gerenciamento de diretiva de grupo** funcionalidade.

    ![Página de recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Sobre o **confirmação** página, clique em **instalar** para instalar a funcionalidade de gerenciamento de diretiva de grupo na máquina virtual. Quando a instalação da funcionalidade for concluída com êxito, clique em **fechar** para sair do **para adicionar funções e funcionalidades** assistente.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Tarefa 3 – iniciar o console de gerenciamento de diretiva de grupo para administrar a política de grupo
Pode usar o console de gerenciamento de diretiva de grupo na máquina de virtual associado a um domínio para administrar a política de grupo no domínio gerido.

> [!NOTE]
> Tem de ser um membro do grupo "Administradores do AAD DC", para administrar a política de grupo no domínio gerido.
>
>

1. No ecrã Iniciar, clique em **ferramentas administrativas**. Deverá ver o **gerenciamento de diretiva de grupo** consola instalada na máquina virtual.

    ![Gerenciamento de diretiva de grupo de lançamento](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Clique em **gerenciamento de diretiva de grupo** para iniciar o console de gerenciamento de diretiva de grupo.

    ![Consola de política de grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Tarefa 4 - Personalizar objetos de diretiva de grupo interno
Existem dois incorporada objetos diretiva de grupo (GPOs) - um para os contentores "Computadores do aad DC" e "Utilizadores do aad DC" no seu domínio gerido. Pode personalizar estes GPOs para configurar a política de grupo no domínio gerido.

1. Na **gerenciamento de diretiva de grupo** consola, clique para expandir a **floresta: contoso100.com** e **domínios** nós para ver as políticas de grupo para o seu domínio gerido.

    ![GPOs incorporadas](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Pode personalizar estes GPOs incorporados para configurar políticas de grupo no seu domínio gerido. Com o botão direito no GPO e clique em **editar...**  para personalizar o GPO incorporado. A ferramenta de Editor de configuração de diretiva de grupo permite-lhe personalizar o GPO.

    ![Editar o GPO incorporado](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Agora, pode utilizar o **Editor de gerenciamento de diretiva de grupo** consola para editar o GPO incorporado. Por exemplo, a captura de ecrã seguinte mostra como personalizar o GPO de "Computadores do aad DC" incorporado.

    ![Personalizar o GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Tarefa 5 - criar um objeto de política de grupo (GPO) personalizado
Pode criar ou importar os seus próprios objetos de política de grupo personalizado. Também pode ligar GPOs personalizados a uma UO personalizada que criou no seu domínio gerido. Para obter mais informações sobre a criação de unidades organizacionais personalizadas, consulte [criar uma UO personalizada num domínio gerido](create-ou.md).

> [!NOTE]
> Tem de ser um membro do grupo "Administradores do AAD DC", para administrar a política de grupo no domínio gerido.
>
>

1. Na **gerenciamento de diretiva de grupo** consola, clique para selecionar sua personalizada (OU unidade organizacional). A UO com o botão direito e clique em **criar um GPO neste domínio e ligá-lo aqui...** .

    ![Criar um GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Especifique um nome para o novo GPO e clique em **OK**.

    ![Especifique um nome para o GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Um novo GPO é criado e ligado à sua UO personalizado. Com o botão direito no GPO e clique em **editar...**  no menu.

    ![GPO recém-criado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Pode personalizar o GPO recém-criado com o **Editor de gerenciamento de diretiva de grupo**.

    ![Personalizar o novo GPO](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Obter mais informações sobre como utilizar [consola de gestão de políticas de grupo](https://technet.microsoft.com/library/cc753298.aspx) estão disponíveis no Technet.

## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](create-instance.md)
* [Junte-se a uma máquina virtual do Windows Server a um domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Gerir um domínio do Azure AD Domain Services](manage-domain.md)
* [Console de gerenciamento de diretiva de grupo](https://technet.microsoft.com/library/cc753298.aspx)
