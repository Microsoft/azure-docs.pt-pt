---
title: Criar e gerir a política de grupos em Azure AD Domain Services | Microsoft Docs
description: Aprenda a editar os objetos de política de grupo incorporados (GPOs) e crie as suas próprias políticas personalizadas num domínio gerido pelos Serviços de Domínio do Diretório Ativo Azure.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f1f2499c49c4adf16b632bc75c246a28330ad27b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619390"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrar a Política de Grupo num domínio gerido por serviços de domínio do diretório ativo Azure

As definições para objetos de utilizador e computador em Azure Ative Directory Domain Services (Azure AD DS) são frequentemente geridas usando objetos de política de grupo (GPOs). O Azure AD DS inclui GPOs incorporados para os utilizadores da *AADDC* e recipientes *de computadores AADDC.* Você pode personalizar estes GPOs incorporados para configurar a Política de Grupo conforme necessário para o seu ambiente. Membros do grupo de *administradores Azure AD DC* têm privilégios de administração de política de grupo no domínio Azure AD DS, e também podem criar GPOs personalizados e unidades organizacionais (OUs). Mais informações sobre o que é a Política de Grupo e como funciona, consulte [a visão geral da Política de Grupo.][group-policy-overview]

Num ambiente híbrido, as políticas de grupo configuradas num ambiente AD DS no local não são sincronizadas com a Azure AD DS. Para definir definições de configuração para utilizadores ou computadores em Azure AD DS, edite um dos GPOs predefinidos ou crie um GPO personalizado.

Este artigo mostra-lhe como instalar as ferramentas de Gestão de Políticas de Grupo, em seguida, editar os GPOs incorporados e criar GPOs personalizados.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Um VM de gestão de servidores do Windows que se junta ao domínio gerido Azure AD DS.
    * Se necessário, complete o tutorial para [criar um VM do Servidor do Windows e junte-o a um domínio gerido][create-join-windows-vm].
* Uma conta de utilizador que é membro do grupo de administradores da *Ad DC Azure* no seu inquilino AZure AD.

> [!NOTE]
> Pode utilizar modelos administrativos de política de grupo copiando os novos modelos para a estação de trabalho de gestão. Copie os ficheiros *.admx* `%SYSTEMROOT%\PolicyDefinitions` e copie os ficheiros *.adml* específicos do local para `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` , onde corresponde ao `Language-CountryRegion` idioma e região dos ficheiros *.adml.*
>
> Por exemplo, copie a versão inglesa e americana dos ficheiros *.adml* na `\en-us` pasta.
>
> Em alternativa, pode armazenar centralmente o seu Modelo Administrativo de Política de Grupo nos controladores de domínio que fazem parte do domínio gerido. Para obter mais informações, consulte [Como criar e gerir a Loja Central para Modelos Administrativos de Política de Grupo no Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Instalar ferramentas de gestão de políticas de grupo

Para criar e configurar o Objeto de Política de Grupo (GPOs), é necessário instalar as ferramentas de Gestão de Políticas de Grupo. Estas ferramentas podem ser instaladas como uma funcionalidade no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas num cliente Windows, consulte instalar [Ferramentas de Administração de Servidor Remoto (RSAT)][install-rsat].

1. Inscreva-se na sua gestão VM. Para obter etapas sobre como ligar utilizando o portal Azure, consulte [Connect to a Windows Server VM][connect-windows-server-vm].
1. **O Gestor do Servidor** deverá abrir por predefinição quando iniciar sposição no VM. Caso contrário, no menu **Iniciar,** selecione **Server Manager**.
1. No *painel de instrumentos* da janela **'Gestor do servidor',** selecione **Adicionar Funções e Funcionalidades**.
1. Na **página antes** de começar a página do Assistente de *Funções e Funcionalidades adicionais*, selecione **Seguinte**.
1. Para o *Tipo de Instalação*, deixe a opção **de instalação baseada em funções ou baseada em recursos** verificada e selecione **Seguinte**.
1. Na página **'Seleção** do Servidor', escolha o VM atual a partir da piscina do servidor, como *myvm.aaddscontoso.com,* selecione **Next**.
1. Na página **'Funções do Servidor',** clique em **'Seguinte'.**
1. Na página **'Funcionalidades',** selecione a função **de Gestão de Políticas de Grupo.**

    ![Instale a "Gestão de Políticas de Grupo" a partir da página Funcionalidades](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Na página **De Confirmação,** selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas de Gestão de Políticas de Grupo.
1. Quando a instalação da funcionalidade estiver concluída, selecione **Close** to exit the **Add Roles and Features** wizard.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Abra a Consola de Gestão de Políticas de Grupo e edite um objeto

Existem objetos de política de grupo predefinidos (GPOs) para utilizadores e computadores num domínio gerido. Com a funcionalidade de Gestão de Políticas de Grupo instalada na secção anterior, vamos ver e editar um GPO existente. Na secção seguinte, cria-se um GPO personalizado.

> [!NOTE]
> Para administrar a política de grupo num domínio gerido, tem de ser inscrito numa conta de utilizador que é membro do grupo de *Administradores AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É apresentada uma lista de ferramentas de gestão disponíveis, incluindo **a Gestão da Política** de Grupo instalada na secção anterior.
1. Para abrir a Consola de Gestão de Políticas de Grupo (GPMC), escolha **Gestão de Políticas de Grupo**.

    ![A Consola de Gestão de Políticas de Grupo abre-se pronta para editar objetos de política de grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Existem dois Objetos de Política de Grupo (GPOs) incorporados num domínio gerido - um para o contentor *de computadores AADDC* e outro para o contentor *de Utilizadores AADDC.* Pode personalizar estes GPOs para configurar a política de grupo conforme necessário dentro do seu domínio gerido.

1. Na consola **de Gestão de Políticas** de Grupo, expanda a **Floresta: aaddscontoso.com** nó. Em seguida, expandir os nós **de Domínios.**

    Existem dois recipientes incorporados para *computadores AADDC* e *utilizadores AADDC.* Cada um destes recipientes tem uma GPO padrão aplicada a eles.

    ![GPOs incorporados aplicados aos recipientes padrão 'AADDC Computers' e 'AADDC Users'](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Estes GPOs incorporados podem ser personalizados para configurar políticas específicas de grupo no seu domínio gerido. Selecione um dos GPOs, como *AADDC Computers GPO,* em seguida, escolha **Edit...**.

    ![Escolha a opção de 'Editar' um dos GPOs incorporados](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. A ferramenta Do Editor de Gestão de Políticas de Grupo abre-se para permitir a personalização do GPO, como *políticas de conta:*

    ![Screenshot do Editor de Gestão de Políticas de Grupo.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Quando terminar, escolha **'>', guarde** para guardar a política. Os computadores atualizam a Política de Grupo por predefinição a cada 90 minutos e aplicam as alterações efetuadas.

## <a name="create-a-custom-group-policy-object"></a>Criar um objeto de política de grupo personalizado

Para agrupar definições de políticas semelhantes, muitas vezes criaSGOs adicionais em vez de aplicar todas as definições necessárias no GPO único e predefinido. Com o Azure AD DS, pode criar ou importar os seus próprios objetos de política de grupo personalizados e ligá-los a um OU personalizado. Se precisar primeiro de criar um OU personalizado, consulte [criar um OU personalizado num domínio gerido.](create-ou.md)

1. Na consola **de Gestão de Políticas** de Grupo, selecione a sua unidade organizacional personalizada (OU), como *myCustomOU*. Selecione à direita o OU e escolha **Criar um GPO neste domínio, e link-lo aqui...**

    ![Crie um GPO personalizado na consola de Gestão de Políticas de Grupo](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Especifique um nome para o novo GPO, como *o Meu GPO personalizado,* em seguida, selecione **OK**. Você pode opcionalmente basear este GPO personalizado em um GPO existente e conjunto de opções de política.

    ![Especifique um nome para o novo GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. O GPO personalizado é criado e ligado ao seu OU personalizado. Para configurar agora as definições de política, selecione à direita o GPO personalizado e escolha **Editar...**

    ![Escolha a opção de 'Editar' o seu GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. O **Editor de Gestão de Políticas de Grupo** abre-se para lhe permitir personalizar o GPO:

    ![Personalize o GPO para configurar as definições necessárias](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Quando terminar, escolha **'>', guarde** para guardar a política. Os computadores atualizam a Política de Grupo por predefinição a cada 90 minutos e aplicam as alterações efetuadas.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as definições de Política de Grupo disponíveis que possa configurar utilizando a Consola de Gestão de Políticas de Grupo, consulte [itens de preferência de Gestão de Políticas de Grupo][group-policy-console].

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
