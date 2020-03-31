---
title: Criar e gerir a política de grupo sintetizar nos Serviços de Domínio Da Azure AD [ Microsoft Docs
description: Aprenda a editar os objetos políticos de grupo incorporados (GPOs) e crie as suas próprias políticas personalizadas num domínio gerido pelo Azure Ative Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: bce71355eef19ec3cc85525033274f57b1a3e0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946413"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Administrar a Política de Grupo num domínio gerido pelos Serviços de Domínio Azure AD

As definições para objetos de utilizador e de computador nos Serviços de Domínio do Diretório Ativo do Azure (Azure AD DS) são frequentemente geridas utilizando objetos de política de grupo (GPOs). O Azure AD DS inclui GPOs incorporados para os *utilizadores AADDC* e recipientes de *computadores AADDC.* Pode personalizar estes GPOs incorporados para configurar a Política de Grupo supérs em caso de necessidade para o seu ambiente. Os membros do grupo de administradores da *AD DC do Azure* têm privilégios de administração de políticas de grupo no domínio Azure AD DS, e também podem criar GPOs personalizados e unidades organizacionais (OUs). Mais informações sobre o que é a Política de Grupos e sobre o seu funcionamento, consulte a visão geral da [Política do Grupo.][group-policy-overview]

Num ambiente híbrido, as políticas de grupo configuradas num ambiente AD DS no local não são sincronizadas para o Azure AD DS. Para definir as definições de configuração para utilizadores ou computadores em DS AD Azure, editar um dos GPOs padrão ou criar um GPO personalizado.

Este artigo mostra-lhe como instalar as ferramentas de Gestão de Políticas de Grupo, depois editar os GPOs incorporados e criar GPOs personalizados.

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

> [!NOTE]
> Pode utilizar modelos administrativos de política de grupo copiando os novos modelos para a estação de trabalho de gestão. Copie os ficheiros `%SYSTEMROOT%\PolicyDefinitions` *.admx* e copie os `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`ficheiros `Language-CountryRegion` *.adml* específicos do local para, onde corresponda ao idioma e região dos ficheiros *.adml.*
>
> Por exemplo, copiar a versão inglesa e americana dos `\en-us` ficheiros *.adml* na pasta.
>
> Em alternativa, pode armazenar centralmente o seu Modelo Administrativo de Política de Grupo nos controladores de domínio que fazem parte do domínio gerido pelo Azure AD DS. Para mais informações, consulte [como criar e gerir a Loja Central para Modelos Administrativos](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)de Política de Grupo no Windows .

## <a name="install-group-policy-management-tools"></a>Instalar ferramentas de Gestão de Políticas de Grupo

Para criar e configurar o Objeto político do grupo (GPOs), precisa de instalar as ferramentas de Gestão de Políticas do Grupo. Estas ferramentas podem ser instaladas como uma funcionalidade no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas num cliente windows, consulte a instalação de Ferramentas de Administração do [Servidor Remoto (RSAT)][install-rsat].

1. Inscreva-se na sua VM de gestão. Para obter passos sobre como se conectar utilizando o portal Azure, consulte [Connect to a Windows Server VM][connect-windows-server-vm].
1. **O Gestor do Servidor** deve abrir por padrão quando iniciar sessão no VM. Caso contrário, no menu **Iniciar,** selecione **Server Manager**.
1. No painel do *Dashboard* da janela Do Gestor do **Servidor,** selecione **Adicionar Funções e Funcionalidades**.
1. Na página Antes de **Iniciar** o Assistente de *Adicionar Funções e Funcionalidades,* selecione **Next**.
1. Para o Tipo de *Instalação,* deixe a opção **de instalação baseada em funções** ou baseada em funções verificada e selecione **Next**.
1. Na página de Seleção do **Servidor,** escolha o VM atual a partir do conjunto de servidores, como *myvm.aaddscontoso.com,* em seguida, selecione **Next**.
1. Na página Funções do **Servidor,** clique em **Seguinte**.
1. Na página **Funcionalidades,** selecione a funcionalidade de Gestão de Políticas do **Grupo.**

    ![Instale a 'Gestão de Políticas de Grupo' a partir da página Funcionalidades](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Na página **confirmação,** selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas de Gestão de Políticas do Grupo.
1. Quando a instalação da funcionalidade estiver concluída, selecione **Close** to exit the **Add Roles and Features** wizard.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Abra a Consola de Gestão de Políticas do Grupo e edite um objeto

Existem objetos de política de grupo padrão (GPOs) para utilizadores e computadores num domínio gerido pelo Azure AD DS. Com a funcionalidade de Gestão de Políticas de Grupo instalada na secção anterior, vamos ver e editar um GPO existente. Na secção seguinte, cria-se um GPO personalizado.

> [!NOTE]
> Para administrar a política de grupo num domínio gerido pelo Azure AD DS, deve ser inscrito numa conta de utilizador que seja membro do grupo de administradores da *AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É apresentada uma lista de ferramentas de gestão disponíveis, incluindo **a Gestão de Políticas** do Grupo instaladas na secção anterior.
1. Para abrir a Consola de Gestão de Políticas do Grupo (GPMC), escolha gestão de políticas de **grupo.**

    ![A Consola de Gestão de Políticas de Grupo abre-se pronta para editar objetos de política de grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Existem dois Objetos políticos de grupo incorporados (GPOs) num domínio gerido por Azure AD DS - um para o recipiente *AADDC Computers* e um para o recipiente *AADDC Users.* Pode personalizar estes GPOs para configurar a política de grupo supérs dentro do seu domínio gerido pelo Azure AD DS.

1. Na consola de **Gestão** de Políticas de Grupo, expanda a **Floresta: aaddscontoso.com** nó. Em seguida, expandir os nós **de Domínios.**

    Existem dois recipientes incorporados para *computadores AADDC* e *utilizadores AADDC*. Cada um destes recipientes tem um GPO padrão aplicado a eles.

    ![GPOs incorporados aplicados aos recipientes 'AADDC Computers' e 'Utilizadores AADDC'](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Estes GPOs incorporados podem ser personalizados para configurar políticas específicas de grupo no seu domínio gerido pelo Azure AD DS. Selecione um dos GPOs, como gpo de *computadores AADDC,* em seguida, escolha **Editar...**.

    ![Escolha a opção de 'Editar' um dos GPOs incorporados](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. A ferramenta Group Policy Management Editor abre-se para permitir personalizar o GPO, tais como Políticas de *Conta:*

    ![Personalize o GPO para configurar as definições conforme necessário](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Quando terminar, escolha **File > Guardar** para salvar a apólice. Os computadores atualizam a Política do Grupo por padrão a cada 90 minutos e aplicam as alterações que fez.

## <a name="create-a-custom-group-policy-object"></a>Criar um objeto de política de grupo personalizado

Para agrupar configurações de políticas semelhantes, muitas vezes cria GPOs adicionais em vez de aplicar todas as definições necessárias no GPO único e padrão. Com o Azure AD DS, pode criar ou importar objetos de política de grupo personalizados e ligá-los a um OU personalizado. Se precisar primeiro de criar um OU personalizado, consulte criar um OU personalizado num domínio gerido por [Azure AD DS](create-ou.md).

1. Na consola **de Gestão** de Políticas de Grupo, selecione a sua unidade organizacional personalizada (OU), como *myCustomOU*. Selecione o OU e escolha **Criar um GPO neste domínio, e link-o aqui...**

    ![Criar um GPO personalizado na consola de Gestão de Políticas de Grupo](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Especifique um nome para o novo GPO, como *o meu GPO personalizado,* em seguida, selecione **OK**. Você pode opcionalmente basear este GPO personalizado em um GPO existente e conjunto de opções políticas.

    ![Especifique um nome para o novo GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. O GPO personalizado é criado e ligado ao seu OU personalizado. Para agora configurar as definições de política, selecione o GPO personalizado e escolha **Editar...**

    ![Escolha a opção de 'Editar' o seu GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. O Editor de **Gestão de Políticas de Grupo** abre-se para permitir personalizar o GPO:

    ![Personalize o GPO para configurar as definições conforme necessário](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Quando terminar, escolha **File > Guardar** para salvar a apólice. Os computadores atualizam a Política do Grupo por padrão a cada 90 minutos e aplicam as alterações que fez.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as definições de Política de Grupo disponíveis que pode configurar utilizando a Consola de Gestão de Políticas de Grupo, consulte [O Trabalho com itens preferenciais][group-policy-console]de política de grupo .

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
