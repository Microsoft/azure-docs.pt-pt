---
title: Ativar o serviço de perfil de utilizador sharePoint com Azure AD DS / Microsoft Docs
description: Saiba como configurar um domínio gerido pelo Azure Ative Directory Domain Services para suportar a sincronização de perfis para o SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: a684a669c491e35b5c6b62dd318b4fe61edeb52b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655385"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Configure Azure Ative Directory Domain Services para apoiar a sincronização do perfil do utilizador para o SharePoint Server

O SharePoint Server inclui um serviço para sincronizar os perfis dos utilizadores. Esta funcionalidade permite que os perfis dos utilizadores sejam armazenados num local central e acessíveis em vários sites e quintas do SharePoint. Para configurar o serviço de perfil de utilizador do SharePoint Server, as permissões adequadas devem ser concedidas num domínio gerido pelo Azure Ative Directory Domain Services (Azure AD DS). Para mais informações, consulte a sincronização do perfil do [utilizador no SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx).

Este artigo mostra-lhe como configurar o Azure AD DS para permitir o serviço de sincronização de perfis do utilizador do SharePoint Server.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, complete o tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Um VM de gestão do Servidor Windows que se junta ao domínio gerido pelo Azure AD DS.
    * Se necessário, complete o tutorial para criar um VM de [gestão.][tutorial-create-management-vm]
* Uma conta de utilizador que é membro do grupo de administradores da *Azure AD DC* no seu inquilino Azure AD.
* Uma conta de serviço SharePoint para o serviço de sincronização de perfis do utilizador.
    * Se necessário, consulte [plan para contas administrativas e de serviço no SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Visão geral das contas de serviço

Num domínio gerido pela Azure AD DS, existe um grupo de segurança chamado **AAD DC Service Accounts** como parte da unidade organizacional *dos Utilizadores* (OU). Os membros deste grupo de segurança são delegados os seguintes privilégios:

- **Replicar o privilégio de Alterações de Diretório** na raiz da DSE.
- **Replicate Directy Changes** privilege no`cn=configuration` contexto de nomeação de *configuração* (recipiente).

O grupo de segurança **AAD DC Service Accounts** é também membro do grupo incorporado **Acesso Compatível Pré-Windows 2000.**

Quando adicionada a este grupo de segurança, a conta de serviço para o serviço de sincronização de perfis do utilizador SharePoint Server recebe os privilégios necessários para funcionar corretamente.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Ativar suporte para sincronização de perfil do utilizador do SharePoint Server

A conta de serviço do SharePoint Server necessita de privilégios adequados para replicar alterações no diretório e deixar o sincronizado de perfil do utilizador do SharePoint Server funcionar corretamente. Para proporcionar estes privilégios, adicione a conta de serviço utilizada para a sincronização do perfil do utilizador sharePoint ao grupo De contade saciedade **aD DC.**

A partir do seu VM de gestão Azure AD DS, complete os seguintes passos:

> [!NOTE]
> Para editar a adesão ao grupo num domínio gerido pelo Azure AD DS, deve ser inscrito numa conta de utilizador que seja membro do grupo de administradores da *AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É mostrada uma lista de ferramentas de gestão disponíveis que foram instaladas no tutorial para criar um VM de [gestão.][tutorial-create-management-vm]
1. Para gerir a adesão ao grupo, selecione **Ative Directory Administrative Center** da lista de ferramentas administrativas.
1. No painel esquerdo, escolha o seu domínio gerido azure AD DS, como *aaddscontoso.com*. É apresentada uma lista de OUs e recursos existentes.
1. Selecione os **Utilizadores** OU e, em seguida, escolha o grupo de segurança *AAD DC Accounts.*
1. Selecione **Membros,** em seguida, escolha **Adicionar...**.
1. Introduza o nome da conta de serviço SharePoint e, em seguida, selecione **OK**. No exemplo seguinte, a conta de serviço SharePoint é nomeada *spadmin:*

    ![Adicione a conta de serviço SharePoint ao grupo de segurança de contas de serviço AAD DC](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte as permissões dos Serviços de Domínio do [Diretório Ativo grant ative para sincronização de perfis no SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
