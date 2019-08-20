---
title: Gerenciar o DNS para Azure AD Domain Services | Microsoft Docs
description: Saiba como instalar as ferramentas do servidor DNS para gerenciar o DNS para um Azure Active Directory Domain Services domínio gerenciado.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: iainfou
ms.openlocfilehash: 9279f97d5260eae698d5dbee10e077b71ab01992
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612323"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Administrar o DNS em um domínio Azure AD Domain Services gerenciado

No Azure Active Directory Domain Services (AD DS do Azure), um componente de chave é DNS (resolução de nomes de domínio). O AD DS do Azure inclui um servidor DNS que fornece resolução de nomes para o domínio gerenciado. Esse servidor DNS inclui registros e atualizações de DNS internos para os principais componentes que permitem a execução do serviço.

Ao executar seus próprios aplicativos e serviços, talvez seja necessário criar registros DNS para computadores que não são ingressados no domínio, configurar endereços IP virtuais para balanceadores de carga ou configurar encaminhadores de DNS externos. Os usuários que pertencem ao grupo de *Administradores de DC do AAD* recebem privilégios de administração de DNS no domínio gerenciado AD DS do Azure e podem criar e editar registros DNS personalizados.

Este artigo mostra como instalar as ferramentas do servidor DNS e usar o console do DNS para gerenciar registros.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
    * Se necessário, conclua o tutorial para [criar uma VM do Windows Server e associá-la a um domínio gerenciado][create-join-windows-vm].
* Uma conta de usuário que é membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure AD.

## <a name="install-dns-server-tools"></a>Instalar ferramentas de servidor DNS

Para criar e modificar o DNS, você precisa instalar as ferramentas do servidor DNS. Essas ferramentas podem ser instaladas como um recurso no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas em um cliente Windows, consulte instalar o [ferramentas de administração de servidor remoto (RSAT)][install-rsat].

1. Entre na sua VM de gerenciamento. Para obter as etapas sobre como se conectar usando o portal do Azure, consulte [conectar-se a uma VM do Windows Server][connect-windows-server-vm].
1. **Gerenciador do servidor** deve abrir por padrão quando você entra na VM. Se não estiver, no menu **Iniciar** , selecione **Gerenciador do servidor**.
1. No painel painel da janela **Gerenciador do servidor** , selecione **adicionar funções e recursos**.
1. Na página **antes de começar** do *Assistente para adicionar funções e recursos*, selecione **Avançar**.
1. Para o *tipo de instalação*, deixe a opção de instalação baseada em **função ou recurso** marcada e selecione **Avançar**.
1. Na página **seleção de servidor** , escolha a VM atual no pool de servidores, como *MyVM.contoso.com*, e selecione **Avançar**.
1. Na página **funções de servidor** , clique em **Avançar**.
1. Na página **recursos** , expanda o nó **ferramentas de administração de servidor remoto** e, em seguida, expanda o nó **ferramentas de administração de função** . Selecione o recurso **ferramentas de servidor DNS** na lista de ferramentas de administração de função.

    ![Opte por instalar as ferramentas do servidor DNS na lista de ferramentas de administração de função disponíveis](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Na página **confirmação** , selecione **instalar**. Pode levar um minuto ou dois para instalar as ferramentas de gerenciamento de Política de Grupo.
1. Quando a instalação do recurso estiver concluída, selecione **fechar** para sair do assistente **adicionar funções e recursos** .

## <a name="open-the-dns-management-console-to-administer-dns"></a>Abra o console de gerenciamento do DNS para administrar o DNS

Com as ferramentas do servidor DNS instaladas, você pode administrar os registros DNS no domínio gerenciado AD DS do Azure.

> [!NOTE]
> Para administrar o DNS em um domínio gerenciado AD DS do Azure, você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

1. Na tela iniciar, selecione **Ferramentas administrativas**. Uma lista de ferramentas de gerenciamento disponíveis é mostrada, incluindo o **DNS** instalado na seção anterior. Selecione **DNS** para iniciar o console de gerenciamento do DNS.
1. Na caixa de diálogo **conectar ao servidor DNS** , selecione **o seguinte computador**e, em seguida, insira o nome de domínio DNS do domínio gerenciado, como *contoso.com*:

    ![Conectar-se ao domínio gerenciado AD DS do Azure no console DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. O console DNS se conecta ao domínio gerenciado AD DS do Azure especificado. Expanda as **zonas de pesquisa direta** ou as **zonas de pesquisa inversa** para criar suas entradas DNS necessárias ou editar os registros existentes, conforme necessário.

    ![Console DNS-administrar domínio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Ao gerenciar registros usando as ferramentas do servidor DNS, certifique-se de não excluir ou modificar os registros DNS internos que são usados pelo Azure AD DS. Os registros DNS internos incluem registros DNS de domínio, registros de servidor de nomes e outros registros usados para o local do DC. Se você modificar esses registros, os serviços de domínio serão interrompidos na rede virtual.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como gerenciar o DNS, consulte o [artigo ferramentas de DNS no TechNet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
