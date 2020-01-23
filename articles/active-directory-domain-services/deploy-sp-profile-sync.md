---
title: Habilitar o serviço de perfil de usuário do SharePoint com o Azure AD DS | Microsoft Docs
description: Saiba como configurar um domínio gerenciado Azure Active Directory Domain Services para dar suporte à sincronização de perfil para o SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: f51744d79d34b734c9cc24cf72785a076a91a4f5
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509058"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Configurar Azure Active Directory Domain Services para dar suporte à sincronização de perfil de usuário para o SharePoint Server

O SharePoint Server inclui um serviço para sincronizar perfis de usuário. Esse recurso permite que os perfis de usuário sejam armazenados em um local central e acessíveis em vários sites e farms do SharePoint. Para configurar o serviço de perfil de usuário do SharePoint Server, as permissões apropriadas devem ser concedidas em um domínio gerenciado Azure Active Directory Domain Services (AD DS do Azure). Para obter mais informações, consulte [sincronização de perfil de usuário no SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx).

Este artigo mostra como configurar o Azure AD DS para permitir o serviço de sincronização de perfil de usuário do SharePoint Server.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
    * Se necessário, conclua o tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
* Uma conta de usuário que é membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure AD.
* Uma conta de serviço do SharePoint para o serviço de sincronização de perfil do usuário.
    * Se necessário, consulte [planejar as contas administrativas e de serviço no SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Visão geral das contas de serviço

Em um domínio gerenciado do Azure AD DS, um grupo de segurança chamado **contas de serviço do AAD DC** existe como parte da UO (unidade organizacional) *dos usuários* . Os membros desse grupo de segurança delegam os seguintes privilégios:

- Privilégio de **replicar alterações de diretório** no DSE raiz.
- Privilégio de **replicar alterações de diretório** no contexto de nomenclatura de *configuração* (`cn=configuration` contêiner).

O grupo de segurança **contas de serviço do AAD DC** também é um membro do grupo interno **acesso compatível com anterior ao Windows 2000**.

Quando adicionado a esse grupo de segurança, a conta de serviço do serviço de sincronização de perfil de usuário do SharePoint Server recebe os privilégios necessários para funcionar corretamente.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Habilitar o suporte para sincronização de perfil de usuário do SharePoint Server

A conta de serviço do SharePoint Server precisa de privilégios adequados para replicar as alterações no diretório e permitir que a sincronização de perfil de usuário do SharePoint Server funcione corretamente. Para fornecer esses privilégios, adicione a conta de serviço usada para a sincronização de perfil de usuário do SharePoint para o grupo de **contas de serviço do AAD DC** .

Em sua VM de gerenciamento de AD DS do Azure, conclua as seguintes etapas:

> [!NOTE]
> Para editar a associação de grupo em um domínio gerenciado AD DS do Azure, você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

1. Na tela iniciar, selecione **Ferramentas administrativas**. É mostrada uma lista de ferramentas de gerenciamento disponíveis que foram instaladas no tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
1. Para gerenciar a associação de grupo, selecione **centro administrativo do Active Directory** na lista de ferramentas administrativas.
1. No painel esquerdo, escolha seu domínio gerenciado AD DS do Azure, como *aadds.contoso.com*. Uma lista de UOs e recursos existentes é mostrada.
1. Selecione a UO **usuários** e, em seguida, escolha o grupo de segurança *contas de serviço do AAD DC* .
1. Selecione **Membros**e, em seguida, escolha **Adicionar...** .
1. Insira o nome da conta de serviço do SharePoint e, em seguida, selecione **OK**. No exemplo a seguir, a conta de serviço do SharePoint é chamada de *admin*:

    ![Adicionar a conta de serviço do SharePoint ao grupo de segurança contas de serviço de DC do AAD](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [conceder permissões de Active Directory Domain Services para a sincronização de perfil no SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
